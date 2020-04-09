---
title: WebAssembly Blazor do núcleo de ASP.NET seguro
author: guardrex
description: Saiba como Blazor proteger aplicativos WebAssemlby como SPAs (Single Page Applications, aplicações de página única).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538979"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="f3ed1-103">WebAssembly Blazor do núcleo de ASP.NET seguro</span><span class="sxs-lookup"><span data-stu-id="f3ed1-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="f3ed1-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f3ed1-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="f3ed1-105">Os aplicativos WebAssembly são protegidos da mesma forma que os SPAs (Single Page Applications, aplicativos de página única).</span><span class="sxs-lookup"><span data-stu-id="f3ed1-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="f3ed1-106">Existem várias abordagens para autenticar usuários para SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo OAuth 2.0](https://oauth.net/), como [o Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="f3ed1-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="f3ed1-107">Biblioteca de autenticação</span><span class="sxs-lookup"><span data-stu-id="f3ed1-107">Authentication library</span></span>

Blazor<span data-ttu-id="f3ed1-108">O WebAssembly suporta autenticar e autorizar aplicativos usando `Microsoft.AspNetCore.Components.WebAssembly.Authentication` OIDC através da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="f3ed1-109">A biblioteca fornece um conjunto de primitivos para autenticação perfeita contra ASP.NET backends core.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="f3ed1-110">A biblioteca integra ASP.NET Identidade Central com suporte à autorização de API construído em cima do [Identity Server](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="f3ed1-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="f3ed1-111">A biblioteca pode autenticar-se contra qualquer Provedor de Identidade (IP) de terceiros que suporte o OIDC, que são chamados de Provedores OpenID (OP).</span><span class="sxs-lookup"><span data-stu-id="f3ed1-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="f3ed1-112">O suporte de Blazor autenticação no WebAssembly é construído em cima da biblioteca *oidc-client.js,* que é usada para lidar com os detalhes do protocolo de autenticação subjacente.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="f3ed1-113">Existem outras opções para autenticar SPAs, como o uso de cookies do SameSite.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="f3ed1-114">No entanto, Blazor o design de engenharia do WebAssembly é estabelecido no OAuth e o OIDC como a melhor opção para autenticação em Blazor aplicativos WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="f3ed1-115">[A autenticação baseada em tokens](xref:security/anti-request-forgery#token-based-authentication) com base em [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida em vez [de autenticação baseada em cookies](xref:security/anti-request-forgery#cookie-based-authentication) por razões funcionais e de segurança:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="f3ed1-116">O uso de um protocolo baseado em tokens oferece uma área de superfície de ataque menor, já que os tokens não são enviados em todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="f3ed1-117">Os pontos finais do servidor não exigem proteção contra [csrf (Cross-Site Request Forgery, solicitação](xref:security/anti-request-forgery) de solicitação de site) porque os tokens são enviados explicitamente.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="f3ed1-118">Isso permite que Blazor você hospede aplicativos do WebAssembly ao lado de aplicativos de páginas MVC ou Razor.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="f3ed1-119">Os tokens têm permissões mais estreitas que os cookies.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="f3ed1-120">Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="f3ed1-121">Os tokens têm uma vida útil curta, uma hora por padrão, o que limita a janela de ataque.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="f3ed1-122">Os tokens também podem ser revogados a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="f3ed1-123">JWTs autônomos oferecem garantias ao cliente e ao servidor sobre o processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="f3ed1-124">Por exemplo, um cliente tem os meios para detectar e validar que os tokens que recebe são legítimos e foram emitidos como parte de um determinado processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="f3ed1-125">Se um terceiro tentar trocar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="f3ed1-126">Os tokens com OAuth e OIDC não dependem que o agente do usuário se comporte corretamente para garantir que o aplicativo esteja seguro.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="f3ed1-127">Protocolos baseados em tokens, como OAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="f3ed1-128">Processo de autenticação com OIDC</span><span class="sxs-lookup"><span data-stu-id="f3ed1-128">Authentication process with OIDC</span></span>

<span data-ttu-id="f3ed1-129">A `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca oferece vários primitivos para implementar autenticação e autorização usando OIDC.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="f3ed1-130">Em termos gerais, a autenticação funciona da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="f3ed1-131">Quando um usuário anônimo seleciona o botão [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) de login ou solicita uma página com o atributo aplicado, o usuário é redirecionado para a página de login do aplicativo (`/authentication/login`).</span><span class="sxs-lookup"><span data-stu-id="f3ed1-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="f3ed1-132">Na página de login, a biblioteca de autenticação se prepara para um redirecionamento para o ponto final da autorização.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="f3ed1-133">O ponto final da Blazor autorização está fora do aplicativo WebAssembly e pode ser hospedado em uma origem separada.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="f3ed1-134">O ponto final é responsável por determinar se o usuário está autenticado e por emitir um ou mais tokens em resposta.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="f3ed1-135">A biblioteca de autenticação fornece um retorno de chamada de login para receber a resposta de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="f3ed1-136">Se o usuário não for autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente é ASP.NET Identidade Central.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="f3ed1-137">Se o usuário já foi autenticado, o ponto final da autorização gera os tokens apropriados`/authentication/login-callback`e redireciona o navegador de volta para o ponto final de retorno de chamada de login ().</span><span class="sxs-lookup"><span data-stu-id="f3ed1-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="f3ed1-138">Quando Blazor o aplicativo WebAssembly carrega o ponto`/authentication/login-callback`final de retorno de chamada de login (), a resposta de autenticação é processada.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="f3ed1-139">Se o processo de autenticação for concluído com sucesso, o usuário será autenticado e enviado opcionalmente de volta à URL protegida original que o usuário solicitou.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="f3ed1-140">Se o processo de autenticação falhar por qualquer motivo,`/authentication/login-failed`o usuário será enviado para a página de falha de login (), e um erro será exibido.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="f3ed1-141">Suporte pré-renderização com autenticação</span><span class="sxs-lookup"><span data-stu-id="f3ed1-141">Support prerendering with authentication</span></span>

<span data-ttu-id="f3ed1-142">Depois de seguir a orientação Blazor em um dos tópicos do aplicativo WebAssembly hospedado, use as seguintes instruções para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="f3ed1-143">Prerenderiza caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="f3ed1-144">Não prerenderiza caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="f3ed1-145">Na classe do `Program` aplicativo Cliente *(Program.cs),* fatorar registros comuns de `ConfigureCommonServices`serviços em um método separado (por exemplo, ):</span><span class="sxs-lookup"><span data-stu-id="f3ed1-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="f3ed1-146">No aplicativo `Startup.ConfigureServices`Server, registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="f3ed1-147">No método do `Startup.Configure` aplicativo Server, substitua por: `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`</span><span class="sxs-lookup"><span data-stu-id="f3ed1-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="f3ed1-148">No aplicativo Servidor, crie uma pasta *Páginas* se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="f3ed1-149">Crie uma página *_Host.cshtml* dentro da pasta *Páginas* do aplicativo Servidor.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="f3ed1-150">Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo Cliente no arquivo *Páginas/_Host.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="f3ed1-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="f3ed1-151">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-151">Update the file's contents:</span></span>

* <span data-ttu-id="f3ed1-152">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="f3ed1-153">Substitua `<app>Loading...</app>` a tag pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="f3ed1-154">Opções para aplicativos hospedados e provedores de login de terceiros</span><span class="sxs-lookup"><span data-stu-id="f3ed1-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="f3ed1-155">Ao autenticar e autorizar um Blazor aplicativo WebAssembly hospedado com um provedor de terceiros, existem várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="f3ed1-156">Qual você escolhe depende do seu cenário.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="f3ed1-157">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="f3ed1-158">Autenticar usuários para chamar apenas APIs de terceiros protegidos</span><span class="sxs-lookup"><span data-stu-id="f3ed1-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="f3ed1-159">Autenticar o usuário com um fluxo OAuth do lado do cliente contra o provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="f3ed1-160">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-160">In this scenario:</span></span>

* <span data-ttu-id="f3ed1-161">O servidor que hospeda o aplicativo não desempenha um papel.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="f3ed1-162">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="f3ed1-163">O aplicativo só pode chamar APIs de terceiros protegidos.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="f3ed1-164">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e em terceiros</span><span class="sxs-lookup"><span data-stu-id="f3ed1-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="f3ed1-165">Configurar identidade com um provedor de login de terceiros.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="f3ed1-166">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="f3ed1-167">Quando um usuário faz login, a Identidade coleta tokens de acesso e atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="f3ed1-168">Nesse ponto, existem algumas abordagens disponíveis para fazer chamadas de API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="f3ed1-169">Use um token de acesso ao servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="f3ed1-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="f3ed1-170">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros a partir de um ponto final da API do servidor.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="f3ed1-171">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente da Identidade no cliente.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="f3ed1-172">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-172">We don't recommend this approach.</span></span> <span data-ttu-id="f3ed1-173">Essa abordagem requer o tratamento do token de acesso de terceiros como se fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="f3ed1-174">Em termos OAuth, o aplicativo público não tem um segredo de cliente porque não é confiável para armazenar segredos com segurança, e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="f3ed1-175">Um cliente confidencial é um cliente que tem um segredo de cliente e é assumido ser capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="f3ed1-176">O token de acesso de terceiros pode receber escopos adicionais para realizar operações sensíveis com base no fato de que o terceiro emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="f3ed1-177">Da mesma forma, os tokens de atualização não devem ser emitidos a um cliente que não é confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em prática.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="f3ed1-178">Faça chamadas de API do cliente para a API do servidor, a fim de chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="f3ed1-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="f3ed1-179">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="f3ed1-180">A partir do servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="f3ed1-181">Embora essa abordagem exija um salto de rede extra através do servidor para chamar uma API de terceiros, ela resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="f3ed1-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="f3ed1-182">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="f3ed1-183">O aplicativo não pode vazar tokens de acesso do servidor que possam conter permissões mais sensíveis.</span><span class="sxs-lookup"><span data-stu-id="f3ed1-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
