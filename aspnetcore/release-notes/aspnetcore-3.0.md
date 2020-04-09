---
title: O que há de novo em ASP.NET Core 3.0
author: rick-anderson
description: Conheça os novos recursos do ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976970"
---
# <a name="whats-new-in-aspnet-core-30"></a>O que há de novo em ASP.NET Core 3.0

Este artigo destaca as mudanças mais significativas no ASP.NET Núcleo 3.0 com links para documentação relevante.

## Blazor

Blazoré uma nova estrutura no ASP.NET Core para construir uma ui web interativa do lado do cliente com .NET:

* Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.
* Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.
* Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.

Blazorcenários suportados por estruturas:

* Componentes de iu de ui reutilizáveis (componentes de navalha)
* Roteamento do lado do cliente
* Layouts de componentes
* Suporte para injeção de dependência
* Formulários e validação
* Construa bibliotecas de componentes com bibliotecas de classe Razor
* Interoperabilidade do JavaScript

Para obter mais informações, consulte <xref:blazor/index>.

### <a name="opno-locblazor-server"></a>BlazorServidor

Blazordesacoplar a lógica de renderização de componentes de como as atualizações de ida e futura são aplicadas. BlazorO servidor oferece suporte para hospedar componentes Razor no servidor em um aplicativo ASP.NET Core. As atualizações de iu SignalR de ui são tratadas por uma conexão. BlazorO servidor é suportado em ASP.NET Núcleo 3.0.

### <a name="opno-locblazor-webassembly-preview"></a>BlazorWebAssembly (Visualização)

Blazoraplicativos também podem ser executados diretamente no navegador usando um tempo de execução .NET baseado no WebAssembly. BlazorO WebAssembly está em pré-visualização e *não* é suportado em ASP.NET Core 3.0. BlazorO WebAssembly será suportado em um futuro lançamento do ASP.NET Core.

### <a name="razor-components"></a>Componentes de navalha

Blazoraplicativos são construídos a partir de componentes. Os componentes são pedaços independentes da interface do usuário (UI), como uma página, diálogo ou formulário. Os componentes são classes .NET normais que definem a lógica de renderização de ui e os manipuladores de eventos do lado do cliente. Você pode criar aplicativos web interativos ricos sem JavaScript.

Os componentes são Blazor tipicamente de autoria usando a sintaxe razor, uma mistura natural de HTML e C#. Os componentes da navalha são semelhantes às páginas de navalha e visualizações de MVC, pois ambos usam Razor. Ao contrário de páginas e visualizações, que são baseadas em um modelo de solicitação-resposta, os componentes são usados especificamente para lidar com a composição da IU.

## <a name="grpc"></a>gRPC

[gRPC:](https://grpc.io/)

* É uma estrutura rpc (chamada de procedimento remoto) popular e de alto desempenho.
* Oferece uma abordagem opinativo de contrato-primeiro para o desenvolvimento da API.
* Usa tecnologias modernas como:

  * HTTP/2 para transporte.
  * Buffers de protocolo como a linguagem de descrição da interface.
  * Formato de serialização binário.
* Fornece recursos como:

  * Autenticação
  * Fluxo bidirecional e controle de fluxo.
  * Cancelamento e intervalos.

A funcionalidade gRPC no ASP.NET Core 3.0 inclui:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; Uma estrutura ASP.NET Core para hospedagem de serviços gRPC. O gRPC no ASP.NET Core integra-se aos recursos padrão do ASP.NET Core, como registro, injeção de dependência (DI), autenticação e autorização.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Um cliente gRPC para .NET Core `HttpClient`que se baseia no familiar .
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client `HttpClientFactory`integration with .

Para obter mais informações, consulte <xref:grpc/index>.

## SignalR

Consulte [ SignalR O código de atualização](xref:migration/22-to-30#signalr) para obter instruções de migração. SignalRagora `System.Text.Json` usa para serializar/desserializar mensagens JSON. Consulte [Alternar para Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json`para obter instruções para restaurar o serializador baseado em newton.

Nos Clientes JavaScript e SignalR.NET para , o suporte foi adicionado para reconexão automática. Por padrão, o cliente tenta se reconectar imediatamente e tentar novamente após 2, 10 e 30 segundos, se necessário. Se o cliente se reconectar com sucesso, ele receberá um novo ID de conexão. Reconexão automática é opt-in:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Os intervalos de reconexão podem ser especificados passando por uma matriz de durações baseadas em milissegundos:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Uma implementação personalizada pode ser aprovada para controle total dos intervalos de reconexão.

Se a reconexão falhar após o último intervalo de reconexão:

* O cliente considera que a conexão está offline.
* O cliente para de tentar se reconectar.

Durante as tentativas de reconexão, atualize a ui do aplicativo para notificar o usuário de que a reconexão está sendo tentada.

Para fornecer feedback de iu quando SignalR a conexão é interrompida, a API do cliente foi expandida para incluir os seguintes manipuladores de eventos:

* `onreconnecting`: Dá aos desenvolvedores a oportunidade de desativar a ui ou de informar aos usuários que o aplicativo está offline.
* `onreconnected`: Dá aos desenvolvedores a oportunidade de atualizar a ui assim que a conexão for restabelecida.

O código `onreconnecting` a seguir é usa para atualizar a ida e volta enquanto tenta conectar:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

O código `onreconnected` a seguir é usa para atualizar a ia de ida e volta na conexão:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR3.0 e, posteriormente, fornece um recurso personalizado para os manipuladores de autorização quando um método de hub requer autorização. O recurso é `HubInvocationContext`uma instância de . O `HubInvocationContext` inclui o:

* `HubCallerContext`
* Nome do método hub sendo invocado.
* Argumentos para o método hub.

Considere o seguinte exemplo de um aplicativo de sala de bate-papo que permite o login de várias organizações via Azure Active Directory. Qualquer pessoa com uma conta microsoft pode fazer login no chat, mas apenas membros da organização proprietária podem banir usuários ou visualizar históricos de bate-papo dos usuários. O aplicativo poderia restringir certas funcionalidades de usuários específicos.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

No código anterior, `DomainRestrictedRequirement` serve `IAuthorizationRequirement`como um costume . Como `HubInvocationContext` o parâmetro de recursos está sendo passado, a lógica interna pode:

* Inspecione o contexto em que o Hub está sendo chamado.
* Tome decisões sobre permitir que o usuário execute métodos individuais do Hub.

Os métodos individuais do Hub podem ser marcados com o nome da política que o código verifica em tempo de execução. À medida que os clientes `DomainRestrictedRequirement` tentam chamar métodos individuais do Hub, o manipulador executa e controla o acesso aos métodos. Com base na `DomainRestrictedRequirement` forma como os controles acessam:

* Todos os usuários conectados podem `SendMessage` chamar o método.
* Somente usuários que fizeram login `@jabbr.net` com um endereço de e-mail podem visualizar os históricos dos usuários.
* Só `bob42@jabbr.net` pode banir os usuários da sala de bate-papo.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

A `DomainRestricted` criação da política pode envolver:

* Em *Startup.cs*, adicionando a nova política.
* Forneça o `DomainRestrictedRequirement` requisito personalizado como parâmetro.
* Registrando-se `DomainRestricted` com o middleware de autorização.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalRhubs usam [Endpoint Routing](xref:fundamentals/routing). SignalRa conexão do hub foi feita anteriormente explicitamente:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Na versão anterior, os desenvolvedores precisavam conectar controladores, páginas de navalha e hubs em uma variedade de lugares. A conexão explícita resulta em uma série de segmentos de roteamento quase idênticos:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalROs hubs 3.0 podem ser roteados através do roteamento de ponto final. Com o roteamento de ponto final, normalmente todo o roteamento pode ser configurado em `UseRouting`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Núcleo 3.0 SignalR adicionado:

Streaming de cliente para servidor. Com o streaming de cliente para servidor, os métodos `IAsyncEnumerable<T>` `ChannelReader<T>`do lado do servidor podem tomar instâncias de um ou . Na seguinte amostra C#, o `UploadStream` método no Hub receberá um fluxo de strings do cliente:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Os aplicativos clientes .NET `ChannelReader<T>` podem `stream` passar uma `UploadStream` `IAsyncEnumerable<T>` ou instância como o argumento do método Hub acima.

Depois `for` que o loop for concluído e a função local sair, a conclusão do fluxo é enviada:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Os aplicativos cliente SignalR `Subject` JavaScript usam o (ou um `stream` Assunto `UploadStream` [RxJS)](https://rxjs.dev/api/index/class/Subject)para o argumento do método Hub acima.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

O código JavaScript `subject.next` poderia usar o método para lidar com as strings à medida que elas são capturadas e prontas para serem enviadas ao servidor.

```javascript
subject.next("example");
subject.complete();
```

Usando códigos como os dois trechos anteriores, experiências de streaming em tempo real podem ser criadas.

## <a name="new-json-serialization"></a>Nova serialização JSON

ASP.NET Core 3.0 <xref:System.Text.Json> agora usa por padrão para serialização JSON:

* Lê e escreve JSON assíncronamente.
* É otimizado para texto UTF-8.
* Tipicamente desempenho `Newtonsoft.Json`superior a .

Para adicionar Json.NET ao ASP.NET Core 3.0, consulte Adicionar suporte ao [formato JSON baseado em Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Novas diretivas de Razor

A lista a seguir contém novas diretivas de Razor:

* [`@attribute`](xref:mvc/views/razor#attribute)&ndash; A `@attribute` diretiva aplica o atributo dado à classe da página ou exibição gerada. Por exemplo, `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements)&ndash; A `@implements` diretiva implementa uma interface para a classe gerada. Por exemplo, `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 suporta autenticação e autorização para APIs e SPAs da Web

ASP.NET Core 3.0 oferece autenticação em SPAs (Single Page Apps, aplicativos de página única) usando o suporte para autorização de API web. ASP.NET Identidade Central para autenticar e armazenar usuários é combinada com [o IdentityServer4](https://identityserver.io/) para implementar o Open ID Connect.

IdentityServer4 é uma estrutura OpenID Connect e OAuth 2.0 para ASP.NET Core 3.0. Ele habilita os seguintes recursos de segurança:

* Autenticação como serviço (AaaS)
* SSO (Single Sign-on/off) (SSO) sobre vários tipos de aplicativos
* Controle de acesso para APIs
* Gateway da Federação

Para obter mais informações, consulte [a documentação IdentityServer4](http://docs.identityserver.io/en/latest/index.html) ou [autenticação e autorização para SPAs](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Autenticação de Certificado e Kerberos

A autenticação do certificado requer:

* Configurando o servidor para aceitar certificados.
* Adicionando o middleware `Startup.Configure`de autenticação em .
* Adicionando o serviço `Startup.ConfigureServices`de autenticação de certificados em .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

As opções de autenticação de certificados incluem a capacidade de:

* Aceite certificados auto-assinados.
* Verifique a revogação do certificado.
* Verifique se o certificado oferecido tem as bandeiras de uso corretas nele.

Um principal usuário padrão é construído a partir das propriedades do certificado. O principal usuário contém um evento que permite complementar ou substituir o principal. Para obter mais informações, consulte <xref:security/authentication/certauth>.

[A autenticação do Windows](/windows-server/security/windows-authentication/windows-authentication-overview) foi estendida para linux e macOS. Nas versões anteriores, a Autenticação do Windows era limitada ao [IIS](xref:host-and-deploy/iis/index) e [ao HttpSys](xref:fundamentals/servers/httpsys). Em ASP.NET Core 3.0, o [Kestrel](xref:fundamentals/servers/kestrel) tem a capacidade de usar o Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM no Windows,](/windows-server/security/kerberos/ntlm-overview)Linux e macOS para hosts com domínio do Windows. O suporte kestrel desses esquemas de autenticação é fornecido pelo pacote [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Como acontece com os outros serviços de autenticação, configure o aplicativo de autenticação em largura e configure o serviço:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Requisitos do host:

* Os hosts do Windows devem ter SPNs [(Service Principal Names, nomes principais](/windows/win32/ad/service-principal-names) de serviço) adicionados à conta de usuário que hospeda o aplicativo.
* As máquinas Linux e macOS devem ser unidas ao domínio.
  * As SPNs devem ser criadas para o processo web.
  * [Os arquivos keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devem ser gerados e configurados na máquina host.

Para obter mais informações, consulte <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Alterações de modelo

Os modelos de IU da Web (Razor Pages, MVC com controlador e visualizações) têm os seguintes removedores:

* A ui de consentimento de cookies não está mais incluída. Para habilitar o recurso de consentimento de cookies em <xref:security/gdpr>um aplicativo ASP.NET gerado pelo modelo Core 3.0, consulte .
* Scripts e ativos estáticos relacionados são agora referenciados como arquivos locais em vez de usar CDNs. Para obter mais informações, consulte [Scripts e ativos estáticos relacionados são agora referenciados como arquivos locais em vez de usar CDNs com base no ambiente atual (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).

O modelo Angular atualizado para usar Angular 8.

O modelo de biblioteca de classe Razor (RCL) é padrão para o desenvolvimento de componentes Razor por padrão. Uma nova opção de modelo no Visual Studio fornece suporte a modelos para páginas e visualizações. Ao criar uma RCL a partir do `--support-pages-and-views` modelo`dotnet new razorclasslib --support-pages-and-views`em um shell de comando, passe a opção ( ).

## <a name="generic-host"></a>Host Genérico

Os modelos ASP.NET Core <xref:fundamentals/host/generic-host>3.0 usam . Versões <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>anteriores utilizadas . O uso do .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host () oferece uma melhor integração dos aplicativos ASP.NET Core com outros cenários de servidor que não são específicos da Web. Para obter mais informações, consulte [HostBuilder substitui WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configuração do host

Antes do lançamento do ASP.NET Core 3.0, `ASPNETCORE_` as variáveis de ambiente prefixadas com foram carregadas para a configuração do host do Web Host. Em 3.0, `AddEnvironmentVariables` é usado para carregar `DOTNET_` variáveis de `CreateDefaultBuilder`ambiente prefixadas com para configuração do host com .

### <a name="changes-to-startup-constructor-injection"></a>Mudanças na injeção de construtor de startup

O Host Genérico só suporta `Startup` os seguintes tipos de injeção de construtor:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Todos os serviços ainda podem ser `Startup.Configure` injetados diretamente como argumentos para o método. Para obter mais informações, consulte [Generic Host restringe a injeção de construtor de inicialização (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* A configuração do Kestrel foi atualizada para a migração para o Host genérico. Em 3.0, o Kestrel é configurado no `ConfigureWebHostDefaults`construtor de host web fornecido por .
* Os adaptadores de conexão foram removidos do Kestrel e substituídos pelo Connection Middleware, que é semelhante ao HTTP Middleware no pipeline ASP.NET Core, mas para conexões de nível inferior.
* A camada de transporte Kestrel foi exposta `Connections.Abstractions`como uma interface pública em .
* A ambiguidade entre cabeçalhos e reboques foi resolvida movendo cabeçalhos para uma nova coleção.
* APIs síncronas de I/O, como `HttpRequest.Body.Read`, são uma fonte comum de fome de thread suscitado síncronos. No 3.0, `AllowSynchronousIO` é desativado por padrão.

Para obter mais informações, consulte <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 ativado por padrão

HTTP/2 é habilitado por padrão em Kestrel para pontos finais HTTPS. O suporte HTTP/2 para IIS ou HTTP.sys é ativado quando suportado pelo sistema operacional.

## <a name="eventcounters-on-request"></a>EventCounters a pedido

O Hosting EventSource, `Microsoft.AspNetCore.Hosting`emite <xref:System.Diagnostics.Tracing.EventCounter> os seguintes novos tipos relacionados às solicitações recebidas:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Roteamento de ponto final

O roteamento de ponto final, que permite que os frameworks (por exemplo, MVC) funcionem bem com middleware, é aprimorado:

* A ordem de middleware e endpoints é configurável `Startup.Configure`no pipeline de processamento de solicitações de .
* Endpoints e middleware compõem bem com outras tecnologias baseadas ASP.NET Core, como health checks.
* Os pontos finais podem implementar uma política, como CORS ou autorização, tanto em middleware quanto em MVC.
* Filtros e atributos podem ser colocados em métodos nos controladores.

Para obter mais informações, consulte <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Verificações de Integridade

As verificações de saúde usam roteamento de ponto final com o Host Genérico. Em `Startup.Configure`, `MapHealthChecks` chamar o construtor de ponto final com a URL de ponto final ou caminho relativo:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Os pontos finais da Verificação de Saúde podem:

* Especifique um ou mais hosts/portas permitidas.
* Requer autorização.
* Requeira o CORS.

Para obter mais informações, consulte os seguintes artigos:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Tubos em HttpContext

Agora é possível ler o corpo de solicitação e <xref:System.IO.Pipelines> escrever o corpo de resposta usando a API. O <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`propriedade fornece <xref:System.IO.Pipelines.PipeReader> um que pode ser usado para ler o corpo de solicitação. O <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`a propriedade <xref:System.IO.Pipelines.PipeWriter> fornece um que pode ser usado para escrever o corpo de resposta. `HttpRequest.BodyReader`é um análogo do `HttpRequest.Body` fluxo. `HttpResponse.BodyWriter`é um análogo do `HttpResponse.Body` fluxo.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Relatório de erro melhorado no IIS

Erros de inicialização ao hospedar aplicativos ASP.NET Core no IIS agora produzem dados de diagnóstico mais ricos. Esses erros são reportados ao Registro de Eventos do Windows com vestígios de pilha sempre que aplicável. Além disso, todos os avisos, erros e exceções não tratadas são registrados no Registro de Eventos do Windows.

## <a name="worker-service-and-worker-sdk"></a>Serviço do Trabalhador e SDK do Trabalhador

O .NET Core 3.0 introduz o novo modelo de aplicativo do Serviço de Trabalhadores. Este modelo fornece um ponto de partida para escrever serviços de longa duração no .NET Core.

Para obter mais informações, consulte:

* [.NET Core Workers como Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Melhorias no Middleware de cabeçalhos encaminhados

Em versões anteriores <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> do ASP.NET Core, chamadas e eram problemáticas quando implantadas em um Azure Linux ou por trás de qualquer proxy reverso que não fosse o IIS. A correção para versões anteriores está documentada no [Forward o esquema para Proxies reversos Linux e não-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Este cenário está fixado em ASP.NET Núcleo 3.0. O host habilita o [Middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando a `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variável ambiente é definida como `true`. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`está definido `true` em nossas imagens de contêiner.

## <a name="performance-improvements"></a>Melhorias de desempenho

ASP.NET Core 3.0 inclui muitas melhorias que reduzem o uso da memória e melhoram o throughput:

* Redução do uso da memória ao usar o recipiente de injeção de dependência incorporado para serviços com escopo.
* Redução de alocações em toda a estrutura, incluindo cenários de middleware e roteamento.
* Redução do uso de memória para conexões WebSocket.
* Redução de memória e melhorias de throughput para conexões HTTPS.
* Novo serializador JSON otimizado e totalmente assíncrono.
* Redução do uso da memória e melhorias de throughput no parsing de formulários.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 só é executado no .NET Core 3.0

A partir de ASP.NET Núcleo 3.0, o .NET Framework não é mais um quadro-alvo suportado. Os projetos direcionados ao .NET Framework podem continuar de forma totalmente suportada usando a [versão LTS .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1). A maioria dos pacotes relacionados ao Core 2.1.x ASP.NET serão suportados indefinidamente, além do período LTS de três anos para o .NET Core 2.1.

Para obter informações de migração, consulte ['Portar seu código de .NET Framework' para .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Use a estrutura compartilhada ASP.NET Core

O ASP.NET framework compartilhado Core 3.0, contido no [metapacote Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)não requer mais um elemento explícito `<PackageReference />` no arquivo do projeto. A estrutura compartilhada é automaticamente referenciada `Microsoft.NET.Sdk.Web` ao usar o SDK no arquivo do projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Assembléias removidas da estrutura compartilhada do Núcleo ASP.NET

As assembléias mais notáveis removidas da estrutura compartilhada do ASP.NET Core 3.0 são:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Para adicionar Json.NET ao ASP.NET Core 3.0, consulte Adicionar suporte ao [formato JSON baseado em Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3.0 `System.Text.Json` é introduzido para leitura e escrita JSON. Para obter mais informações, consulte [a serialização do Novo JSON](#new-json-serialization) neste documento.
* [Entity Framework Core](/ef/core/)

Para obter uma lista completa de conjuntos removidos da estrutura compartilhada, consulte [Assembléias sendo removidas do Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755). Para obter mais informações sobre a motivação para essa mudança, consulte [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) e [A first look to changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 