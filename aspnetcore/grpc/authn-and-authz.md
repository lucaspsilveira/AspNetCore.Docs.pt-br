---
title: Autenticação e autorização no gRPC para ASP.NET Core
author: jamesnk
description: Saiba como usar a autenticação e a autorização no gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/authn-and-authz
ms.openlocfilehash: 4216a6f28d7c4fdcd45fbd639617fa99b91e784c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407480"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Autenticação e autorização no gRPC para ASP.NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Autenticar usuários chamando um serviço gRPC

gRPC pode ser usado com a [autenticação ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada chamada.

Veja a seguir um exemplo de `Startup.Configure` que usa a autenticação gRPC e ASP.NET Core:

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
> A ordem na qual você registra o ASP.NET Core de middleware de autenticação é importante. Sempre chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` de e antes `UseEndpoints` .

O mecanismo de autenticação que seu aplicativo usa durante uma chamada precisa ser configurado. A configuração de autenticação é adicionada no `Startup.ConfigureServices` e será diferente dependendo do mecanismo de autenticação usado pelo seu aplicativo. Para obter exemplos de como proteger aplicativos ASP.NET Core, consulte [exemplos de autenticação](xref:security/authentication/samples).

Depois que a autenticação tiver sido configurada, o usuário poderá ser acessado em um método de serviço gRPC por meio do `ServerCallContext` .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Autenticação de token de portador

O cliente pode fornecer um token de acesso para autenticação. O servidor valida o token e o usa para identificar o usuário.

No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

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

Configurar `ChannelCredentials` o em um canal é uma maneira alternativa de enviar o token para o serviço com chamadas gRPC. A credencial é executada cada vez que uma chamada gRPC é feita, o que evita a necessidade de escrever código em vários locais para passar o token por conta própria.

A credencial no exemplo a seguir configura o canal para enviar o token com cada chamada gRPC:

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

### <a name="client-certificate-authentication"></a>Autenticação de certificado do cliente

Um cliente pode, opcionalmente, fornecer um certificado de cliente para autenticação. A [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core. Quando a solicitação entra ASP.NET Core, o [pacote de autenticação de certificado de cliente](xref:security/authentication/certauth) permite que você resolva o certificado para um `ClaimsPrincipal` .

> [!NOTE]
> Configure o servidor para aceitar certificados de cliente. Para obter informações sobre como aceitar certificados de cliente no Kestrel, no IIS e no Azure, consulte <xref:security/authentication/certauth#configure-your-server-to-require-certificates> .

No cliente .NET gRPC, o certificado do cliente é adicionado a `HttpClientHandler` que é usado para criar o cliente gRPC:

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
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Outros mecanismos de autenticação

Muitos ASP.NET Core mecanismos de autenticação com suporte funcionam com o gRPC:

* Azure Active Directory
* Certificado do Cliente
* IdentityServer
* JWT Token
* OAuth 2.0
* OpenID Connect
* O certificado do provedor de identidade do Web Services Federation

Para obter mais informações sobre como configurar a autenticação no servidor, consulte [ASP.NET Core autenticação](xref:security/authentication/identity).

Configurar o cliente gRPC para usar a autenticação dependerá do mecanismo de autenticação que você está usando. Os exemplos anteriores de token de portador e certificado de cliente mostram duas maneiras que o cliente gRPC pode ser configurado para enviar metadados de autenticação com chamadas gRPC:

* Clientes gRPC fortemente tipados usam `HttpClient` internamente. A autenticação pode ser configurada em [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)ou adicionando instâncias de [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) personalizadas ao `HttpClient` .
* Cada chamada gRPC tem um `CallOptions` argumento opcional. Cabeçalhos personalizados podem ser enviados usando a coleção de cabeçalhos da opção.

> [!NOTE]
> A autenticação do Windows (NTLM/Kerberos/Negotiate) não pode ser usada com gRPC. gRPC requer HTTP/2, e HTTP/2 não dá suporte à autenticação do Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizar usuários a acessar os serviços e métodos de serviço

Por padrão, todos os métodos em um serviço podem ser chamados por usuários não autenticados. Para exigir autenticação, aplique o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo ao serviço:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Você pode usar os argumentos do construtor e as propriedades do `[Authorize]` atributo para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas. Por exemplo, se você tiver uma política de autorização personalizada chamada `MyAuthorizationPolicy` , certifique-se de que somente os usuários que correspondem a essa política possam acessar o serviço usando o seguinte código:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Os métodos de serviço individuais também podem ter o `[Authorize]` atributo aplicado. Se o usuário atual não **corresponder às políticas aplicadas ao método** e à classe, um erro será retornado para o chamador:

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

* [Autenticação de token de portador no ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configurar a autenticação de certificado de cliente no ASP.NET Core](xref:security/authentication/certauth)
