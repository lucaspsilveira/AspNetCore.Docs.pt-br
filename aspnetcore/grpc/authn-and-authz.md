---
title: Autenticação e autorização em gRPC para ASP.NET Core
author: jamesnk
description: Saiba como usar autenticação e autorização em gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78964437"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Autenticação e autorização em gRPC para ASP.NET Core

Por [James Newton-King](https://twitter.com/jamesnk)

[Exibir ou baixar o código da amostra](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Autenticar usuários chamando um serviço gRPC

o gRPC pode ser usado com [ASP.NET autenticação Core](xref:security/authentication/identity) para associar um usuário a cada chamada.

A seguir, um `Startup.Configure` exemplo de que usa a autenticação gRPC e ASP.NET Core:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> A ordem em que você registra o middleware de autenticação ASP.NET Core importa. Sempre `UseAuthentication` ligue `UseAuthorization` `UseRouting` e `UseEndpoints`depois e antes.

O mecanismo de autenticação que seu aplicativo usa durante uma chamada precisa ser configurado. A configuração de `Startup.ConfigureServices` autenticação é adicionada e será diferente dependendo do mecanismo de autenticação que seu aplicativo usa. Para exemplos de como proteger aplicativos ASP.NET Core, consulte [amostras de autenticação](xref:security/authentication/samples).

Uma vez configurada a autenticação, o usuário pode ser acessado em `ServerCallContext`métodos de serviço gRPC através do .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Autenticação de token do portador

O cliente pode fornecer um token de acesso para autenticação. O servidor valida o token e o usa para identificar o usuário.

No servidor, a autenticação do token do portador é configurada usando o [middleware JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

No cliente .NET gRPC, o token pode ser enviado com chamadas como um cabeçalho:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

Configurar `ChannelCredentials` em um canal é uma maneira alternativa de enviar o token para o serviço com chamadas gRPC. A credencial é executada cada vez que uma chamada gRPC é feita, o que evita a necessidade de escrever código em vários lugares para passar o token você mesmo.

A credencial no exemplo a seguir configura o canal para enviar o token a cada chamada gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

Um cliente poderia, alternativamente, fornecer um certificado de cliente para autenticação. [A autenticação](https://tools.ietf.org/html/rfc5246#section-7.4.4) do certificado acontece no nível TLS, muito antes de chegar ao ASP.NET Core. Quando a solicitação entra em ASP.NET Core, o [pacote de autenticação](xref:security/authentication/certauth) do certificado do cliente permite que você resolva o certificado para um `ClaimsPrincipal`.

> [!NOTE]
> O host precisa ser configurado para aceitar certificados de cliente. Consulte [configurar seu host para exigir certificados](xref:security/authentication/certauth#configure-your-host-to-require-certificates) para obter informações sobre a aceitação de certificados de cliente em Kestrel, IIS e Azure.

No cliente .NET gRPC, o certificado `HttpClientHandler` do cliente é adicionado a isso é usado para criar o cliente gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Outros mecanismos de autenticação

Muitos mecanismos de autenticação suportados pelo ASP.NET Core funcionam com o gRPC:

* Azure Active Directory
* Certificado do Cliente
* Servidor de identidade
* JWT Token
* OAuth 2.0
* OpenID Connect
* O certificado do provedor de identidade do Web Services Federation

Para obter mais informações sobre a configuração da autenticação no servidor, consulte [ASP.NET autenticação Core](xref:security/authentication/identity).

A configuração do cliente gRPC para usar a autenticação dependerá do mecanismo de autenticação que você está usando. Os exemplos anteriores de token e certificado de cliente mostram algumas maneiras pelas quais o cliente gRPC pode ser configurado para enviar metadados de autenticação com chamadas gRPC:

* Os clientes gRPC `HttpClient` fortemente digitados usam internamente. A autenticação pode ser configurada no [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou adicionando instâncias [httpmessagehandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas à `HttpClient`.
* Cada chamada gRPC `CallOptions` tem um argumento opcional. Cabeçalhos personalizados podem ser enviados usando a coleção de cabeçalhos da opção.

> [!NOTE]
> A autenticação do Windows (NTLM/Kerberos/Negotiate) não pode ser usada com gRPC. o gRPC requer HTTP/2 e o HTTP/2 não suporta autenticação do Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizar os usuários a acessar serviços e métodos de serviço

Por padrão, todos os métodos em um serviço podem ser chamados por usuários não autenticados. Para exigir autenticação, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplique o atributo ao serviço:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Você pode usar os argumentos e `[Authorize]` propriedades do construtor do atributo para restringir o acesso apenas aos usuários que correspondem a políticas de [autorização](xref:security/authorization/policies)específicas . Por exemplo, se você tiver `MyAuthorizationPolicy`uma política de autorização personalizada chamada, certifique-se de que apenas os usuários que correspondam a essa diretiva podem acessar o serviço usando o seguinte código:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Métodos de serviço `[Authorize]` individuais também podem ter o atributo aplicado. Se o usuário atual não corresponder às políticas **aplicadas ao** método e à classe, um erro será devolvido ao chamador:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Recursos adicionais

* [Autenticação do Portador Token em ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configure a autenticação do Certificado do Cliente no ASP.NET Core](xref:security/authentication/certauth)
