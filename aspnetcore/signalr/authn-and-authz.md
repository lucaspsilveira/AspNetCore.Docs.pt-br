---
title: Autenticação e autorização no ASP.NET CoreSignalR
author: bradygaster
description: Saiba como usar a autenticação e a autorização no ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 794465ceb69f47ee3d5cc8c100b321cb958d9cfe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407129"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="fa2dc-103">Autenticação e autorização no ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="fa2dc-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="fa2dc-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="fa2dc-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="fa2dc-105">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="fa2dc-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="fa2dc-106">Autenticar usuários que se conectam a um SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="fa2dc-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="fa2dc-107">pode ser usado com [ASP.NET Core autenticação](xref:security/authentication/identity) para associar um usuário a cada conexão.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="fa2dc-108">Em um Hub, os dados de autenticação podem ser acessados da propriedade [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="fa2dc-109">A autenticação permite que o Hub Chame métodos em todas as conexões associadas a um usuário.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="fa2dc-110">Para obter mais informações, consulte [gerenciar usuários e grupos SignalR no ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="fa2dc-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="fa2dc-111">Várias conexões podem ser associadas a um único usuário.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="fa2dc-112">Veja a seguir um exemplo de `Startup.Configure` quais usos SignalR e autenticação de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="fa2dc-113">A ordem na qual você registra o SignalR e o middleware de autenticação ASP.NET Core é importante.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="fa2dc-114">Sempre chame `UseAuthentication` antes `UseSignalR` para que SignalR o tenha um usuário no `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="fa2dc-115">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="fa2dc-115">Cookie authentication</span></span>

<span data-ttu-id="fa2dc-116">Em um aplicativo baseado em navegador, a autenticação de cookie permite que suas credenciais de usuário existentes fluam automaticamente para SignalR conexões.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="fa2dc-117">Ao usar o cliente de navegador, nenhuma configuração adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="fa2dc-118">Se o usuário estiver conectado ao seu aplicativo, a SignalR conexão herdará automaticamente essa autenticação.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="fa2dc-119">Cookies são uma maneira específica do navegador de enviar tokens de acesso, mas os clientes sem navegador podem enviá-los.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="fa2dc-120">Ao usar o [cliente .net](xref:signalr/dotnet-client), a `Cookies` propriedade pode ser configurada na `.WithUrl` chamada para fornecer um cookie.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="fa2dc-121">No entanto, usar a autenticação de cookie do cliente .NET requer que o aplicativo forneça uma API para trocar dados de autenticação para um cookie.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="fa2dc-122">Autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="fa2dc-122">Bearer token authentication</span></span>

<span data-ttu-id="fa2dc-123">O cliente pode fornecer um token de acesso em vez de usar um cookie.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="fa2dc-124">O servidor valida o token e o usa para identificar o usuário.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="fa2dc-125">Essa validação é feita somente quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="fa2dc-126">Durante a vida útil da conexão, o servidor não é revalidado automaticamente para verificar a revogação de tokens.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="fa2dc-127">No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="fa2dc-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="fa2dc-128">No cliente JavaScript, o token pode ser fornecido usando a opção [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="fa2dc-129">No cliente .NET, há uma propriedade [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) semelhante que pode ser usada para configurar o token:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="fa2dc-130">A função de token de acesso que você fornece é chamada antes de **cada** solicitação HTTP feita pelo SignalR .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="fa2dc-131">Se você precisar renovar o token para manter a conexão ativa (porque ela pode expirar durante a conexão), faça isso de dentro dessa função e retorne o token atualizado.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="fa2dc-132">Nas APIs Web padrão, os tokens de portador são enviados em um cabeçalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="fa2dc-133">No entanto, o SignalR não pode definir esses cabeçalhos em navegadores ao usar alguns transportes.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="fa2dc-134">Ao usar Websockets e eventos enviados pelo servidor, o token é transmitido como um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="fa2dc-135">Para dar suporte a isso no servidor, é necessária uma configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="fa2dc-136">A cadeia de caracteres de consulta é usada em navegadores ao se conectar com WebSockets e eventos enviados pelo servidor devido a limitações da API do navegador.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="fa2dc-137">Ao usar HTTPS, os valores de cadeia de caracteres de consulta são protegidos pela conexão TLS.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="fa2dc-138">No entanto, muitos servidores registram valores de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-138">However, many servers log query string values.</span></span> <span data-ttu-id="fa2dc-139">Para obter mais informações, consulte [considerações de segurança SignalR em ASP.NET Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="fa2dc-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="fa2dc-140">usa cabeçalhos para transmitir tokens em ambientes que dão suporte a eles (como os clientes .NET e Java).</span><span class="sxs-lookup"><span data-stu-id="fa2dc-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="fa2dc-141">Cookies versus tokens de portador</span><span class="sxs-lookup"><span data-stu-id="fa2dc-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="fa2dc-142">Os cookies são específicos para os navegadores.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="fa2dc-143">Enviá-los de outros tipos de clientes adiciona complexidade comparada ao envio de tokens de portador.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="fa2dc-144">Consequentemente, a autenticação de cookie não é recomendada, a menos que o aplicativo precise apenas autenticar usuários do cliente de navegador.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="fa2dc-145">A autenticação de token de portador é a abordagem recomendada ao usar clientes diferentes do cliente de navegador.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="fa2dc-146">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="fa2dc-146">Windows authentication</span></span>

<span data-ttu-id="fa2dc-147">Se a [autenticação do Windows](xref:security/authentication/windowsauth) estiver configurada em seu aplicativo, o SignalR poderá usar essa identidade para proteger os hubs.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="fa2dc-148">No entanto, para enviar mensagens a usuários individuais, você precisa adicionar um provedor de ID de usuário personalizado.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="fa2dc-149">O sistema de autenticação do Windows não fornece a declaração de "identificador de nome".</span><span class="sxs-lookup"><span data-stu-id="fa2dc-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="fa2dc-150">usa a declaração para determinar o nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="fa2dc-151">Adicione uma nova classe que implementa `IUserIdProvider` e recupere uma das declarações do usuário para usar como o identificador.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="fa2dc-152">Por exemplo, para usar a declaração "Name" (que é o nome de usuário do Windows no formulário `[Domain]\[Username]` ), crie a seguinte classe:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="fa2dc-153">Em vez de `ClaimTypes.Name` , você pode usar qualquer valor do `User` (como o identificador de SID do Windows e assim por diante).</span><span class="sxs-lookup"><span data-stu-id="fa2dc-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="fa2dc-154">O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="fa2dc-155">Caso contrário, uma mensagem destinada a um usuário poderia acabar indo para um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="fa2dc-156">Registre esse componente em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="fa2dc-157">No cliente .NET, a autenticação do Windows deve ser habilitada definindo a propriedade [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="fa2dc-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="fa2dc-158">A autenticação do Windows tem suporte no Internet Explorer e no Microsoft Edge, mas não em todos os navegadores.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-158">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="fa2dc-159">Por exemplo, no Chrome e no Safari, a tentativa de usar a autenticação do Windows e o WebSocket falha.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-159">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="fa2dc-160">Quando a autenticação do Windows falha, o cliente tenta fazer fallback para outros transportes que podem funcionar.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-160">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="fa2dc-161">Usar declarações para personalizar o tratamento de identidades</span><span class="sxs-lookup"><span data-stu-id="fa2dc-161">Use claims to customize identity handling</span></span>

<span data-ttu-id="fa2dc-162">Um aplicativo que autentica os usuários pode derivar SignalR IDs de usuário de declarações do usuário.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-162">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="fa2dc-163">Para especificar como SignalR o cria IDs de usuário, implemente `IUserIdProvider` e registre a implementação.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-163">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="fa2dc-164">O código de exemplo demonstra como você usaria declarações para selecionar o endereço de email do usuário como a propriedade de identificação.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-164">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="fa2dc-165">O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-165">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="fa2dc-166">Caso contrário, uma mensagem destinada a um usuário poderia acabar indo para um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-166">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="fa2dc-167">O registro de conta adiciona uma declaração com `ClaimsTypes.Email` o tipo ao banco de dados de identidade ASP.net.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-167">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="fa2dc-168">Registre esse componente no seu `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-168">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="fa2dc-169">Autorizar usuários a acessar hubs e métodos de Hub</span><span class="sxs-lookup"><span data-stu-id="fa2dc-169">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="fa2dc-170">Por padrão, todos os métodos em um Hub podem ser chamados por um usuário não autenticado.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-170">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="fa2dc-171">Para exigir autenticação, aplique o atributo [autorizar](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) ao Hub:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-171">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="fa2dc-172">Você pode usar os argumentos do construtor e as propriedades do `[Authorize]` atributo para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-172">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="fa2dc-173">Por exemplo, se você tiver uma política de autorização personalizada chamada, `MyAuthorizationPolicy` poderá garantir que somente os usuários que correspondem a essa política possam acessar o Hub usando o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-173">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="fa2dc-174">Os métodos de Hub individuais também podem ter o `[Authorize]` atributo aplicado.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-174">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="fa2dc-175">Se o usuário atual não corresponder à política aplicada ao método, um erro será retornado ao chamador:</span><span class="sxs-lookup"><span data-stu-id="fa2dc-175">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="fa2dc-176">Usar manipuladores de autorização para personalizar a autorização do método de Hub</span><span class="sxs-lookup"><span data-stu-id="fa2dc-176">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="fa2dc-177">fornece um recurso personalizado para manipuladores de autorização quando um método de Hub requer autorização.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-177"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="fa2dc-178">O recurso é uma instância do `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-178">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="fa2dc-179">O `HubInvocationContext` inclui o `HubCallerContext` , o nome do método de Hub que está sendo invocado e os argumentos para o método de Hub.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-179">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="fa2dc-180">Considere o exemplo de uma sala de chat que permite a entrada de várias organizações por meio de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-180">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="fa2dc-181">Qualquer pessoa com um conta Microsoft pode entrar no chat, mas somente os membros da organização proprietária devem ser capazes de proibir os usuários ou exibir os históricos de chat dos usuários.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-181">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="fa2dc-182">Além disso, talvez queiramos restringir determinadas funcionalidades de determinados usuários.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-182">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="fa2dc-183">Usando os recursos atualizados do ASP.NET Core 3,0, isso é totalmente possível.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-183">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="fa2dc-184">Observe como o `DomainRestrictedRequirement` serve como personalizado `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="fa2dc-184">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="fa2dc-185">Agora que o `HubInvocationContext` parâmetro de recurso está sendo passado, a lógica interna pode inspecionar o contexto no qual o Hub está sendo chamado e tomar decisões sobre como permitir que o usuário execute métodos de Hub individuais.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-185">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="fa2dc-186">No `Startup.ConfigureServices` , adicione a nova política, fornecendo o `DomainRestrictedRequirement` requisito personalizado como um parâmetro para criar a `DomainRestricted` política.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-186">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="fa2dc-187">No exemplo anterior, a `DomainRestrictedRequirement` classe é uma `IAuthorizationRequirement` e sua própria `AuthorizationHandler` para esse requisito.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-187">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="fa2dc-188">É aceitável dividir esses dois componentes em classes separadas para separar as preocupações.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-188">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="fa2dc-189">Um benefício da abordagem do exemplo é que não há necessidade de injetar o `AuthorizationHandler` durante a inicialização, pois o requisito e o manipulador são a mesma coisa.</span><span class="sxs-lookup"><span data-stu-id="fa2dc-189">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fa2dc-190">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fa2dc-190">Additional resources</span></span>

* [<span data-ttu-id="fa2dc-191">Autenticação de token de portador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa2dc-191">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="fa2dc-192">Autorização baseada em recursos</span><span class="sxs-lookup"><span data-stu-id="fa2dc-192">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
