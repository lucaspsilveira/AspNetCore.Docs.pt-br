---
title: Configurar a autenticação de certificado no ASP.NET Core
author: blowdart
description: Saiba como configurar a autenticação de certificado no ASP.NET Core para IIS e HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/certauth
ms.openlocfilehash: 2c58a274e8de0b1205b223287b7690b1d5caed23
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445119"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Configurar a autenticação de certificado no ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate`contém uma implementação semelhante à [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) para ASP.NET Core. A autenticação de certificado ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core. Mais precisamente, esse é um manipulador de autenticação que valida o certificado e, em seguida, fornece um evento no qual você pode resolver esse certificado para um `ClaimsPrincipal` . 

[Configure seu servidor](#configure-your-server-to-require-certificates) para autenticação de certificado, seja ele IIS, Kestrel, aplicativos Web do Azure ou qualquer outra coisa que você esteja usando.

## <a name="proxy-and-load-balancer-scenarios"></a>Cenários de balanceador de carga e proxy

A autenticação de certificado é um cenário com monitoração de estado usado principalmente onde um proxy ou um balanceador de carga não trata o tráfego entre clientes e servidores. Se um proxy ou um balanceador de carga for usado, a autenticação de certificado só funcionará se o proxy ou o balanceador de carga:

* Manipula a autenticação.
* Passa as informações de autenticação do usuário para o aplicativo (por exemplo, em um cabeçalho de solicitação), que atua nas informações de autenticação.

Uma alternativa à autenticação de certificado em ambientes em que proxies e balanceadores de carga são usados é Active Directory serviços federados (ADFS) com o OpenID Connect (OIDC).

## <a name="get-started"></a>Introdução

Adquira um certificado HTTPS, aplique-o e [Configure o servidor](#configure-your-server-to-require-certificates) para exigir certificados.

Em seu aplicativo Web, adicione uma referência ao pacote [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) . Em seguida `Startup.ConfigureServices` , no método, chame `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` com suas opções, fornecendo um delegado para `OnCertificateValidated` fazer qualquer validação suplementar no certificado do cliente enviado com solicitações. Transforme essas informações em um `ClaimsPrincipal` e defina-as na `context.Principal` propriedade.

Se a autenticação falhar, esse manipulador retornará uma `403 (Forbidden)` resposta em vez de a `401 (Unauthorized)` , como você pode esperar. O raciocínio é que a autenticação deve ocorrer durante a conexão de TLS inicial. No momento em que atinge o manipulador, é tarde demais. Não há como atualizar a conexão de uma conexão anônima para uma com um certificado.

Além disso, adicione `app.UseAuthentication();` o `Startup.Configure` método. Caso contrário, o `HttpContext.User` não será definido como `ClaimsPrincipal` criado a partir do certificado. Por exemplo:

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

O exemplo anterior demonstra a maneira padrão de adicionar autenticação de certificado. O manipulador constrói uma entidade de usuário usando as propriedades comuns do certificado.

## <a name="configure-certificate-validation"></a>Configurar validação de certificado

O `CertificateAuthenticationOptions` manipulador tem algumas validações internas que são as validações mínimas que você deve executar em um certificado. Cada uma dessas configurações é habilitada por padrão.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = encadeado, SelfSigned ou todos (encadeados | SelfSigned)

Valor padrão: `CertificateTypes.Chained`

Essa verificação valida que apenas o tipo de certificado apropriado é permitido. Se o aplicativo estiver usando certificados autoassinados, essa opção precisará ser definida como `CertificateTypes.All` ou `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Valor padrão: `true`

Essa verificação valida que o certificado apresentado pelo cliente tem o EKU (uso estendido de chave) de autenticação de cliente ou nenhum EKUs. Como as especificações dizem, se nenhum EKU for especificado, todos os EKUs serão considerados válidos.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Valor padrão: `true`

Essa verificação valida que o certificado está dentro do período de validade. Em cada solicitação, o manipulador garante que um certificado que era válido quando foi apresentado não tenha expirado durante sua sessão atual.

### <a name="revocationflag"></a>RevocationFlag

Valor padrão: `X509RevocationFlag.ExcludeRoot`

Um sinalizador que especifica quais certificados na cadeia são verificados para revogação.

As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.

### <a name="revocationmode"></a>Rerevocationmode

Valor padrão: `X509RevocationMode.Online`

Um sinalizador que especifica como as verificações de revogação são executadas.

A especificação de uma verificação online pode resultar em um longo atraso enquanto a autoridade de certificação é contatada.

As verificações de revogação são executadas somente quando o certificado é encadeado a um certificado raiz.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Posso configurar meu aplicativo para exigir um certificado somente em determinados caminhos?

Isso não é possível. Lembre-se de que a troca de certificado faz com que o início da conversa HTTPS seja feito pelo servidor antes que a primeira solicitação seja recebida nessa conexão, portanto, não é possível fazer o escopo com base em qualquer campo de solicitação.

## <a name="handler-events"></a>Eventos de manipulador

O manipulador tem dois eventos:

* `OnAuthenticationFailed`: Chamado se uma exceção ocorrer durante a autenticação e permitir que você reaja.
* `OnCertificateValidated`: Chamado depois que o certificado foi validado, passou na validação e uma entidade de segurança padrão foi criada. Esse evento permite que você execute sua própria validação e aumente ou substitua a entidade de segurança. Para obter exemplos, inclua:
  * Determinando se o certificado é conhecido por seus serviços.
  * Construindo sua própria entidade de segurança. Considere o exemplo a seguir em `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

Se você achar que o certificado de entrada não atende à sua validação extra, chame `context.Fail("failure reason")` com um motivo de falha.

Para a funcionalidade real, você provavelmente desejará chamar um serviço registrado na injeção de dependência que se conecta a um banco de dados ou a outro tipo de armazenamento de usuário. Acesse seu serviço usando o contexto passado para seu representante. Considere o exemplo a seguir em `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

Conceitualmente, a validação do certificado é um problema de autorização. A adição de um check-in, por exemplo, um emissor ou uma impressão digital em uma política de autorização, em vez de dentro `OnCertificateValidated` , é perfeitamente aceitável.

## <a name="configure-your-server-to-require-certificates"></a>Configurar o servidor para exigir certificados

### <a name="kestrel"></a>Kestrel

No *Program.cs*, configure o Kestrel da seguinte maneira:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> de chamar não terão os padrões aplicados.

### <a name="iis"></a>IIS

Conclua as seguintes etapas no Gerenciador do IIS:

1. Selecione seu site na guia **conexões** .
1. Clique duas vezes na opção **configurações de SSL** na janela exibição de **recursos** .
1. Marque a caixa de seleção **exigir SSL** e selecione o botão de opção **exigir** na seção **certificados do cliente** .

![Configurações de certificado de cliente no IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Proxies da Web personalizados e do Azure

Consulte a [documentação de host e implantação](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) para saber como configurar o middleware de encaminhamento de certificado.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Usar autenticação de certificado em aplicativos Web do Azure

Nenhuma configuração de encaminhamento é necessária para o Azure. Isso já está configurado no middleware de encaminhamento de certificado.

> [!NOTE]
> Isso requer que o CertificateForwardingMiddleware esteja presente.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Usar autenticação de certificado em proxies da Web personalizados

O `AddCertificateForwarding` método é usado para especificar:

* O nome do cabeçalho do cliente.
* Como o certificado deve ser carregado (usando a `HeaderConverter` Propriedade).

Em proxies da Web personalizados, o certificado é passado como um cabeçalho de solicitação personalizado, por exemplo `X-SSL-CERT` . Para usá-lo, configure o encaminhamento de certificado no `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

O `Startup.Configure` método, em seguida, adiciona o middleware. `UseCertificateForwarding`é chamado antes das chamadas para `UseAuthentication` e `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Uma classe separada pode ser usada para implementar a lógica de validação. Como o mesmo certificado autoassinado é usado neste exemplo, certifique-se de que apenas seu certificado possa ser usado. Valide se as impressões digitais do certificado do cliente e do certificado do servidor correspondem, caso contrário, qualquer certificado pode ser usado e será suficiente para autenticar. Isso seria usado dentro do `AddCertificate` método. Você também pode validar o assunto ou o emissor aqui se estiver usando certificados intermediários ou filhos.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Implementar um HttpClient usando um certificado e o HttpClientHandler

O `HttpClientHandler` pode ser adicionado diretamente no construtor da `HttpClient` classe. Deve-se tomar cuidado ao criar instâncias do `HttpClient` . O `HttpClient` enviará o certificado a cada solicitação.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Implementar um HttpClient usando um certificado e um HttpClient nomeado do IHttpClientFactory 

No exemplo a seguir, um certificado de cliente é adicionado a um `HttpClientHandler` usando a `ClientCertificates` Propriedade do manipulador. Esse manipulador pode ser usado em uma instância nomeada de um `HttpClient` usando o `ConfigurePrimaryHttpMessageHandler` método. Isso é configurado em `Startup.ConfigureServices` :

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

O `IHttpClientFactory` pode então ser usado para obter a instância nomeada com o manipulador e o certificado. O `CreateClient` método com o nome do cliente definido na `Startup` classe é usado para obter a instância. A solicitação HTTP pode ser enviada usando o cliente, conforme necessário.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Se o certificado correto for enviado ao servidor, os dados serão retornados. Se nenhum certificado ou o certificado errado for enviado, um código de status HTTP 403 será retornado.

### <a name="create-certificates-in-powershell"></a>Criar certificados no PowerShell

Criar os certificados é a parte mais difícil de configurar esse fluxo. Um certificado raiz pode ser criado usando o `New-SelfSignedCertificate` cmdlet do PowerShell. Ao criar o certificado, use uma senha forte. É importante adicionar o `KeyUsageProperty` parâmetro e o `KeyUsage` parâmetro, conforme mostrado.

#### <a name="create-root-ca"></a>Criar AC raiz

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> O `-DnsName` valor do parâmetro deve corresponder ao destino de implantação do aplicativo. Por exemplo, "localhost" para desenvolvimento.

#### <a name="install-in-the-trusted-root"></a>Instalar na raiz confiável

O certificado raiz precisa ser confiável no sistema host. Um certificado raiz que não foi criado por uma autoridade de certificação não será confiável por padrão. O link a seguir explica como isso pode ser feito no Windows:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Certificado intermediário

Um certificado intermediário agora pode ser criado a partir do certificado raiz. Isso não é necessário para todos os casos de uso, mas talvez seja necessário criar muitos certificados ou precisar ativar ou desativar grupos de certificados. O `TextExtension` parâmetro é necessário para definir o comprimento do caminho nas restrições básicas do certificado.

O certificado intermediário pode ser adicionado ao certificado intermediário confiável no sistema de host do Windows.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Criar certificado filho do certificado intermediário

Um certificado filho pode ser criado a partir do certificado intermediário. Esta é a entidade final e não precisa criar mais certificados filho.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Criar certificado filho do certificado raiz

Um certificado filho também pode ser criado a partir do certificado raiz diretamente. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Exemplo de certificado raiz intermediário-certificado

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

Ao usar os certificados raiz, intermediário ou filho, os certificados podem ser validados usando a impressão digital ou o PublicKey, conforme necessário.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Cache de validação de certificado

ASP.NET Core 5,0 e versões posteriores dão suporte à capacidade de habilitar o cache de resultados de validação. O Caching melhora drasticamente o desempenho da autenticação de certificado, pois a validação é uma operação cara.

Por padrão, a autenticação de certificado desabilita o cache. Para habilitar o Caching, chame `AddCertificateCache` em `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

A implementação de cache padrão armazena resultados na memória. Você pode fornecer seu próprio cache implementando `ICertificateValidationCache` -o e registrando-o com injeção de dependência. Por exemplo, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Certificados de cliente opcionais

Esta seção fornece informações para aplicativos que devem proteger um subconjunto do aplicativo com um certificado. Por exemplo, uma Razor página ou controlador no aplicativo pode exigir certificados de cliente. Isso apresenta desafios como certificados de cliente:
  
* É um recurso TLS, não um recurso HTTP.
* São negociadas por conexão e devem ser negociadas no início da conexão antes que quaisquer dados HTTP estejam disponíveis. No início da conexão, somente o Indicação de Nome de Servidor (SNI) &dagger; é conhecido. Os certificados do cliente e do servidor são negociados antes da primeira solicitação em uma conexão e as solicitações geralmente não são capazes de renegociar.

A renegociação de TLS foi uma maneira antiga de implementar certificados de cliente opcionais. Isso não é mais recomendado porque:
- No HTTP/1.1, a renegociação durante uma solicitação POST pode causar um deadlock em que o corpo da solicitação encheu a janela TCP e os pacotes de renegociação não podem ser recebidos.
- O HTTP/2 [proíbe explicitamente](https://tools.ietf.org/html/rfc7540#section-9.2.1) a renegociação.
- O TLS 1,3 [removeu](https://tools.ietf.org/html/rfc8740#section-1) o suporte para a renegociação.

O ASP.NET Core 5 Preview 4 e posterior adiciona um suporte mais conveniente para certificados de cliente opcionais. Para obter mais informações, consulte o [exemplo de certificados opcionais](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

A seguinte abordagem dá suporte a certificados de cliente opcionais:

* Configure a associação para o domínio e o subdomínio:
  * Por exemplo, configure associações em `contoso.com` e `myClient.contoso.com` . O `contoso.com` host não requer um certificado de cliente, mas sim `myClient.contoso.com` .
  * Para obter mais informações, confira:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Observe que o Kestrel atualmente não dá suporte a várias configurações de TLS em uma associação, você precisará de duas associações com IPs ou portas exclusivas. Consulte https://github.com/dotnet/runtime/issues/31097
    * IIS
      * [Hospedando o IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Configurar a segurança no IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Configurar o Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)
* Para solicitações ao aplicativo Web que exigem um certificado de cliente e não têm uma:
  * Redirecione para a mesma página usando o subdomínio protegido por certificado de cliente.
  * Por exemplo, redirecionar para `myClient.contoso.com/requestedPage` . Como a solicitação para `myClient.contoso.com/requestedPage` é um nome de host diferente de `contoso.com/requestedPage` , o cliente estabelece uma conexão diferente e o certificado do cliente é fornecido.
  * Para obter mais informações, consulte <xref:security/authorization/introduction>.

Deixe perguntas, comentários e outros comentários sobre certificados de cliente opcionais neste problema de [discussão do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .

&dagger;Indicação de Nome de Servidor (SNI) é uma extensão de TLS para incluir um domínio virtual como parte da negociação SSL. Isso efetivamente significa que o nome de domínio virtual, ou um nome de host, pode ser usado para identificar o ponto de extremidade de rede.
