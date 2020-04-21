---
title: ASP.NET Blazor principais cenários de segurança do Core WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661588"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="f01ea-102">ASP.NET Core Blazor WebAssembly cenários adicionais de segurança</span><span class="sxs-lookup"><span data-stu-id="f01ea-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="f01ea-103">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f01ea-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="f01ea-104">Solicite tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="f01ea-104">Request additional access tokens</span></span>

<span data-ttu-id="f01ea-105">A maioria dos aplicativos só requer um token de acesso para interagir com os recursos protegidos que eles usam.</span><span class="sxs-lookup"><span data-stu-id="f01ea-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="f01ea-106">Em alguns cenários, um aplicativo pode exigir mais de um token para interagir com dois ou mais recursos.</span><span class="sxs-lookup"><span data-stu-id="f01ea-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="f01ea-107">No exemplo a seguir, outros escopos de AAD (AAD) do Microsoft Graph AaD são exigidos por um aplicativo para ler dados do usuário e enviar e-mails.</span><span class="sxs-lookup"><span data-stu-id="f01ea-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="f01ea-108">Depois de adicionar as permissões da API do Microsoft Graph no portal Azure AAD, os escopos adicionais são configurados no aplicativo Client (`Program.Main` *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f01ea-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="f01ea-109">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo forneça um token com um determinado conjunto de escopos, como visto no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="f01ea-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="f01ea-110">`TryGetToken`Retorna:</span><span class="sxs-lookup"><span data-stu-id="f01ea-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="f01ea-111">`true`com `token` o para uso.</span><span class="sxs-lookup"><span data-stu-id="f01ea-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="f01ea-112">`false`se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="f01ea-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="f01ea-113">Lidar com erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="f01ea-113">Handle token request errors</span></span>

<span data-ttu-id="f01ea-114">Quando um SPA (Single Page Application, aplicativo de página única) autentica um usuário usando o Open ID Connect (OIDC), o estado de autenticação é mantido localmente dentro do SPA e no Provedor de Identidade (IP) na forma de um cookie de sessão definido como resultado do usuário fornecer suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="f01ea-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="f01ea-115">Os tokens que o IP emite para o usuário normalmente são válidos por curtos períodos de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="f01ea-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="f01ea-116">Caso contrário, o usuário seria logado após o vencimento dos tokens concedidos.</span><span class="sxs-lookup"><span data-stu-id="f01ea-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="f01ea-117">Na maioria dos casos, os clientes da OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="f01ea-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="f01ea-118">Existem alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logon explicitamente fora do IP.</span><span class="sxs-lookup"><span data-stu-id="f01ea-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="f01ea-119">Esse cenário ocorre se `https://login.microsoftonline.com` um usuário visitar e fazer loga. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logout. Qualquer token que o cliente possui pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="f01ea-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="f01ea-120">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário após o término do token atual.</span><span class="sxs-lookup"><span data-stu-id="f01ea-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="f01ea-121">Esses cenários não são específicos para autenticação baseada em tokens.</span><span class="sxs-lookup"><span data-stu-id="f01ea-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="f01ea-122">Eles fazem parte da natureza dos SPAs.</span><span class="sxs-lookup"><span data-stu-id="f01ea-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="f01ea-123">Um SPA usando cookies também falha em chamar uma API de servidor se o cookie de autenticação for removido.</span><span class="sxs-lookup"><span data-stu-id="f01ea-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="f01ea-124">Quando um aplicativo executa chamadas de API para recursos protegidos, você deve estar ciente do seguinte:</span><span class="sxs-lookup"><span data-stu-id="f01ea-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="f01ea-125">Para prover um novo token de acesso para chamar a API, o usuário pode ser obrigado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="f01ea-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="f01ea-126">Mesmo que o cliente tenha um token que parece ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="f01ea-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="f01ea-127">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="f01ea-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="f01ea-128">A solicitação é bem sucedida, e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="f01ea-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="f01ea-129">A solicitação falha, e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="f01ea-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="f01ea-130">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="f01ea-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="f01ea-131">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="f01ea-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="f01ea-132">Armazene o estado atual da página no armazenamento da sessão.</span><span class="sxs-lookup"><span data-stu-id="f01ea-132">Store the current page state in session storage.</span></span> <span data-ttu-id="f01ea-133">Durante `OnInitializeAsync`, verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="f01ea-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="f01ea-134">Adicione um parâmetro de seqüência de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa para reidratar o estado previamente salvo.</span><span class="sxs-lookup"><span data-stu-id="f01ea-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="f01ea-135">Adicione um parâmetro de seqüência de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem correr o risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="f01ea-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="f01ea-136">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="f01ea-136">The following example shows how to:</span></span>

* <span data-ttu-id="f01ea-137">Preserve o estado antes de redirecionar para a página de login.</span><span class="sxs-lookup"><span data-stu-id="f01ea-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="f01ea-138">Recupere o estado anterior após a autenticação usando o parâmetro de seqüência de string de consulta.</span><span class="sxs-lookup"><span data-stu-id="f01ea-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="f01ea-139">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="f01ea-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="f01ea-140">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="f01ea-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="f01ea-141">Isso pode ser o caso quando você está usando algo como um contêiner de estado e você quer restaurar o estado após o sucesso da autenticação.</span><span class="sxs-lookup"><span data-stu-id="f01ea-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="f01ea-142">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado assim que a operação de autenticação for concluída com sucesso.</span><span class="sxs-lookup"><span data-stu-id="f01ea-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="f01ea-143">`Authentication`componente *(Páginas/Autenticação.navalha):*</span><span class="sxs-lookup"><span data-stu-id="f01ea-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="f01ea-144">Personalizar rotas de aplicativos</span><span class="sxs-lookup"><span data-stu-id="f01ea-144">Customize app routes</span></span>

<span data-ttu-id="f01ea-145">Por padrão, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` a biblioteca usa as rotas mostradas na tabela a seguir para representar diferentes estados de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f01ea-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="f01ea-146">Rota</span><span class="sxs-lookup"><span data-stu-id="f01ea-146">Route</span></span>                            | <span data-ttu-id="f01ea-147">Finalidade</span><span class="sxs-lookup"><span data-stu-id="f01ea-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="f01ea-148">Desencadeia uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="f01ea-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="f01ea-149">Lida com o resultado de qualquer operação de login.</span><span class="sxs-lookup"><span data-stu-id="f01ea-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="f01ea-150">Exibe mensagens de erro quando a operação de login falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="f01ea-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="f01ea-151">Desencadeia uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="f01ea-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="f01ea-152">Lida com o resultado de uma operação de sinalização.</span><span class="sxs-lookup"><span data-stu-id="f01ea-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="f01ea-153">Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="f01ea-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="f01ea-154">Indica que o usuário fez logout com sucesso.</span><span class="sxs-lookup"><span data-stu-id="f01ea-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="f01ea-155">Aciona uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="f01ea-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="f01ea-156">Desencadeia uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="f01ea-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="f01ea-157">As rotas mostradas na tabela `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`anterior são configuráveis via .</span><span class="sxs-lookup"><span data-stu-id="f01ea-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="f01ea-158">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que lida com cada caminho.</span><span class="sxs-lookup"><span data-stu-id="f01ea-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="f01ea-159">No exemplo a seguir, todos os `/security`caminhos são prefixados com .</span><span class="sxs-lookup"><span data-stu-id="f01ea-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="f01ea-160">`Authentication`componente *(Páginas/Autenticação.navalha):*</span><span class="sxs-lookup"><span data-stu-id="f01ea-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f01ea-161">`Program.Main`*(Program.cs):*</span><span class="sxs-lookup"><span data-stu-id="f01ea-161">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="f01ea-162">Se a exigência exige caminhos completamente diferentes, defina `RemoteAuthenticatorView` as rotas como descrito anteriormente e torne-as com um parâmetro de ação explícito:</span><span class="sxs-lookup"><span data-stu-id="f01ea-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="f01ea-163">Você pode dividir a ui em páginas diferentes se você optar por fazê-lo.</span><span class="sxs-lookup"><span data-stu-id="f01ea-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="f01ea-164">Personalize a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="f01ea-164">Customize the authentication user interface</span></span>

<span data-ttu-id="f01ea-165">`RemoteAuthenticatorView`inclui um conjunto padrão de peças de iu para cada estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f01ea-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="f01ea-166">Cada estado pode ser personalizado passando `RenderFragment`em um costume .</span><span class="sxs-lookup"><span data-stu-id="f01ea-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="f01ea-167">Para personalizar o texto exibido durante o processo `RemoteAuthenticatorView` inicial de login, pode alterar o seguinte.</span><span class="sxs-lookup"><span data-stu-id="f01ea-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="f01ea-168">`Authentication`componente *(Páginas/Autenticação.navalha):*</span><span class="sxs-lookup"><span data-stu-id="f01ea-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="f01ea-169">O `RemoteAuthenticatorView` tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="f01ea-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="f01ea-170">Rota</span><span class="sxs-lookup"><span data-stu-id="f01ea-170">Route</span></span>                            | <span data-ttu-id="f01ea-171">Fragmento</span><span class="sxs-lookup"><span data-stu-id="f01ea-171">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
