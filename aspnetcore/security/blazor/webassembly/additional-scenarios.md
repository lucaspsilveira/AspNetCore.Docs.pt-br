---
title: Cenários Blazor de segurança adicionais do webassembly ASP.NET Core
author: guardrex
description: Saiba como configurar Blazor o Webassembly para cenários de segurança adicionais.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 093498c3e0d42430c66c66a0998bcc44f62d1e0d
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206145"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="c0e44-103">Cenários de segurança adicionais do Webassembly ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="c0e44-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="c0e44-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c0e44-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="c0e44-105">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="c0e44-105">Request additional access tokens</span></span>

<span data-ttu-id="c0e44-106">A maioria dos aplicativos requer apenas um token de acesso para interagir com os recursos protegidos que eles usam.</span><span class="sxs-lookup"><span data-stu-id="c0e44-106">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="c0e44-107">Em alguns cenários, um aplicativo pode exigir mais de um token para interagir com dois ou mais recursos.</span><span class="sxs-lookup"><span data-stu-id="c0e44-107">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="c0e44-108">No exemplo a seguir, Azure Active Directory adicionais (AAD) Microsoft Graph escopos de API são exigidos por um aplicativo para ler dados do usuário e enviar email.</span><span class="sxs-lookup"><span data-stu-id="c0e44-108">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="c0e44-109">Depois de adicionar as permissões de API do Microsoft Graph no portal do Azure AAD, os escopos adicionais são configurados no aplicativo cliente (`Program.Main`, *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e44-109">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="c0e44-110">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos, como mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="c0e44-110">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="c0e44-111">`TryGetToken`apresenta</span><span class="sxs-lookup"><span data-stu-id="c0e44-111">`TryGetToken` returns:</span></span>

* <span data-ttu-id="c0e44-112">`true`com o `token` para uso.</span><span class="sxs-lookup"><span data-stu-id="c0e44-112">`true` with the `token` for use.</span></span>
* <span data-ttu-id="c0e44-113">`false`Se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="c0e44-113">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="c0e44-114">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="c0e44-114">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="c0e44-115">O `AuthorizationMessageHandler` serviço pode ser usado com `HttpClient` para anexar tokens de acesso a solicitações de saída.</span><span class="sxs-lookup"><span data-stu-id="c0e44-115">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="c0e44-116">Os tokens são adquiridos `IAccessTokenProvider` usando o serviço existente.</span><span class="sxs-lookup"><span data-stu-id="c0e44-116">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="c0e44-117">Se um token não puder ser adquirido `AccessTokenNotAvailableException` , um será gerado.</span><span class="sxs-lookup"><span data-stu-id="c0e44-117">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="c0e44-118">`AccessTokenNotAvailableException`tem um `Redirect` método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.</span><span class="sxs-lookup"><span data-stu-id="c0e44-118">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="c0e44-119">O `AuthorizationMessageHandler` pode ser configurado com URLs autorizadas, escopos e URL de retorno usando `ConfigureHandler` o método.</span><span class="sxs-lookup"><span data-stu-id="c0e44-119">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="c0e44-120">No exemplo a seguir, `AuthorizationMessageHandler` configura um `HttpClient` in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e44-120">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="c0e44-121">Para sua conveniência, `BaseAddressAuthorizationMessageHandler` é incluído um que é pré-configurado com o endereço base do aplicativo como uma URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="c0e44-121">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="c0e44-122">Os modelos Webassembly do mais alto-habilitado para autenticação agora usam [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) para configurar `HttpClient` um com `BaseAddressAuthorizationMessageHandler`o:</span><span class="sxs-lookup"><span data-stu-id="c0e44-122">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="c0e44-123">Em que o cliente é criado `CreateClient` no exemplo anterior, as instâncias `HttpClient` do são fornecidas que incluem tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="c0e44-123">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="c0e44-124">Em seguida `HttpClient` , o configurado é usado para fazer solicitações autorizadas `try-catch` usando um padrão simples.</span><span class="sxs-lookup"><span data-stu-id="c0e44-124">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="c0e44-125">O componente `FetchData` a seguir solicita dados de previsão do tempo:</span><span class="sxs-lookup"><span data-stu-id="c0e44-125">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="c0e44-126">Como alternativa, você pode definir um cliente tipado que lide com todas as questões de aquisição de token e HTTP em uma única classe:</span><span class="sxs-lookup"><span data-stu-id="c0e44-126">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="c0e44-127">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0e44-127">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="c0e44-128">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0e44-128">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="c0e44-129">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c0e44-129">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="c0e44-130">Manipular erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="c0e44-130">Handle token request errors</span></span>

<span data-ttu-id="c0e44-131">Quando um aplicativo de página única (SPA) autentica um usuário usando o OIDC (Open ID Connect), o estado de autenticação é mantido localmente no SPA e no provedor de identidade (IP) na forma de um cookie de sessão que é definido como resultado do usuário que fornece suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="c0e44-131">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="c0e44-132">Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="c0e44-132">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="c0e44-133">Caso contrário, o usuário será desconectado após os tokens concedidos expirarem.</span><span class="sxs-lookup"><span data-stu-id="c0e44-133">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="c0e44-134">Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="c0e44-134">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="c0e44-135">Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP.</span><span class="sxs-lookup"><span data-stu-id="c0e44-135">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="c0e44-136">Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="c0e44-136">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="c0e44-137">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.</span><span class="sxs-lookup"><span data-stu-id="c0e44-137">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="c0e44-138">Esses cenários não são específicos para a autenticação baseada em token.</span><span class="sxs-lookup"><span data-stu-id="c0e44-138">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="c0e44-139">Eles fazem parte da natureza do SPAs.</span><span class="sxs-lookup"><span data-stu-id="c0e44-139">They are part of the nature of SPAs.</span></span> <span data-ttu-id="c0e44-140">Um SPA que usa cookies também falha ao chamar uma API de servidor se o cookie de autenticação for removido.</span><span class="sxs-lookup"><span data-stu-id="c0e44-140">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="c0e44-141">Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="c0e44-141">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="c0e44-142">Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="c0e44-142">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="c0e44-143">Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="c0e44-143">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="c0e44-144">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="c0e44-144">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="c0e44-145">A solicitação é realizada com sucesso e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="c0e44-145">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="c0e44-146">A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="c0e44-146">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="c0e44-147">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="c0e44-147">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="c0e44-148">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="c0e44-148">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="c0e44-149">Armazene o estado da página atual no armazenamento de sessão.</span><span class="sxs-lookup"><span data-stu-id="c0e44-149">Store the current page state in session storage.</span></span> <span data-ttu-id="c0e44-150">Durante `OnInitializeAsync`, verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="c0e44-150">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="c0e44-151">Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c0e44-151">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="c0e44-152">Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="c0e44-152">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="c0e44-153">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="c0e44-153">The following example shows how to:</span></span>

* <span data-ttu-id="c0e44-154">Preserve o estado antes de redirecionar para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="c0e44-154">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="c0e44-155">Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="c0e44-155">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="c0e44-156">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="c0e44-156">Save app state before an authentication operation</span></span>

<span data-ttu-id="c0e44-157">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="c0e44-157">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="c0e44-158">Esse pode ser o caso quando você estiver usando algo como um contêiner de estado e desejar restaurar o estado depois que a autenticação for realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="c0e44-158">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="c0e44-159">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="c0e44-159">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="c0e44-160">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c0e44-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="c0e44-161">Personalizar rotas de aplicativo</span><span class="sxs-lookup"><span data-stu-id="c0e44-161">Customize app routes</span></span>

<span data-ttu-id="c0e44-162">Por padrão, a `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="c0e44-162">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="c0e44-163">Rota</span><span class="sxs-lookup"><span data-stu-id="c0e44-163">Route</span></span>                            | <span data-ttu-id="c0e44-164">Finalidade</span><span class="sxs-lookup"><span data-stu-id="c0e44-164">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="c0e44-165">Dispara uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="c0e44-165">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="c0e44-166">Manipula o resultado de qualquer operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="c0e44-166">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="c0e44-167">Exibe mensagens de erro quando a operação de entrada falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="c0e44-167">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="c0e44-168">Dispara uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="c0e44-168">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="c0e44-169">Lida com o resultado de uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="c0e44-169">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="c0e44-170">Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="c0e44-170">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="c0e44-171">Indica que o usuário fez logoff com êxito.</span><span class="sxs-lookup"><span data-stu-id="c0e44-171">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="c0e44-172">Dispara uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="c0e44-172">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="c0e44-173">Dispara uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="c0e44-173">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="c0e44-174">As rotas mostradas na tabela anterior são configuráveis `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`via.</span><span class="sxs-lookup"><span data-stu-id="c0e44-174">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="c0e44-175">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.</span><span class="sxs-lookup"><span data-stu-id="c0e44-175">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="c0e44-176">No exemplo a seguir, todos os caminhos são prefixados `/security`com.</span><span class="sxs-lookup"><span data-stu-id="c0e44-176">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="c0e44-177">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c0e44-177">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="c0e44-178">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e44-178">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="c0e44-179">Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o `RemoteAuthenticatorView` com um parâmetro de ação explícita:</span><span class="sxs-lookup"><span data-stu-id="c0e44-179">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="c0e44-180">Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.</span><span class="sxs-lookup"><span data-stu-id="c0e44-180">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="c0e44-181">Personalizar a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="c0e44-181">Customize the authentication user interface</span></span>

<span data-ttu-id="c0e44-182">`RemoteAuthenticatorView`inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="c0e44-182">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="c0e44-183">Cada Estado pode ser personalizado passando um personalizado `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="c0e44-183">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="c0e44-184">Para personalizar o texto exibido durante o processo de logon inicial, o pode `RemoteAuthenticatorView` alterar o da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="c0e44-184">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="c0e44-185">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c0e44-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="c0e44-186">O `RemoteAuthenticatorView` tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="c0e44-186">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="c0e44-187">Rota</span><span class="sxs-lookup"><span data-stu-id="c0e44-187">Route</span></span>                            | <span data-ttu-id="c0e44-188">Fragmento</span><span class="sxs-lookup"><span data-stu-id="c0e44-188">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="c0e44-189">Personalizar o usuário</span><span class="sxs-lookup"><span data-stu-id="c0e44-189">Customize the user</span></span>

<span data-ttu-id="c0e44-190">Os usuários associados ao aplicativo podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="c0e44-190">Users bound to the app can be customized.</span></span> <span data-ttu-id="c0e44-191">No exemplo a seguir, todos os usuários autenticados recebem `amr` uma declaração para cada um dos métodos de autenticação do usuário.</span><span class="sxs-lookup"><span data-stu-id="c0e44-191">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="c0e44-192">Crie uma classe que estenda a `RemoteUserAccount` classe:</span><span class="sxs-lookup"><span data-stu-id="c0e44-192">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="c0e44-193">Crie uma fábrica que estenda `AccountClaimsPrincipalFactory<TAccount>`:</span><span class="sxs-lookup"><span data-stu-id="c0e44-193">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="c0e44-194">Registre os serviços para usar `CustomAccountFactory`o:</span><span class="sxs-lookup"><span data-stu-id="c0e44-194">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="c0e44-195">Suporte ao pré-processamento com autenticação</span><span class="sxs-lookup"><span data-stu-id="c0e44-195">Support prerendering with authentication</span></span>

<span data-ttu-id="c0e44-196">Depois de seguir as diretrizes em um dos tópicos Blazor do aplicativo Webassembly hospedado, use as instruções a seguir para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="c0e44-196">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="c0e44-197">Processa caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="c0e44-197">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="c0e44-198">Não PreRender caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="c0e44-198">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="c0e44-199">Na classe do `Program` aplicativo cliente (*Program.cs*), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="c0e44-199">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

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

<span data-ttu-id="c0e44-200">No aplicativo do `Startup.ConfigureServices`servidor, registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="c0e44-200">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="c0e44-201">No método do aplicativo do `Startup.Configure` servidor, substitua `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="c0e44-201">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="c0e44-202">No aplicativo de servidor, crie uma pasta de *páginas* se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="c0e44-202">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="c0e44-203">Crie uma página *_Host. cshtml* dentro da pasta *Páginas* do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="c0e44-203">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="c0e44-204">Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo cliente no arquivo *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c0e44-204">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="c0e44-205">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="c0e44-205">Update the file's contents:</span></span>

* <span data-ttu-id="c0e44-206">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c0e44-206">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="c0e44-207">Substitua a `<app>Loading...</app>` marca pela seguinte:</span><span class="sxs-lookup"><span data-stu-id="c0e44-207">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="c0e44-208">Opções para aplicativos hospedados e provedores de logon de terceiros</span><span class="sxs-lookup"><span data-stu-id="c0e44-208">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="c0e44-209">Ao autenticar e autorizar um Blazor aplicativo Webassembly hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="c0e44-209">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="c0e44-210">O que você escolher dependerá de seu cenário.</span><span class="sxs-lookup"><span data-stu-id="c0e44-210">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="c0e44-211">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="c0e44-211">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="c0e44-212">Autenticar usuários para chamar somente APIs de terceiros protegidas</span><span class="sxs-lookup"><span data-stu-id="c0e44-212">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="c0e44-213">Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="c0e44-213">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="c0e44-214">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="c0e44-214">In this scenario:</span></span>

* <span data-ttu-id="c0e44-215">O servidor que hospeda o aplicativo não desempenha uma função.</span><span class="sxs-lookup"><span data-stu-id="c0e44-215">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="c0e44-216">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="c0e44-216">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="c0e44-217">O aplicativo só pode chamar APIs de terceiros protegidas.</span><span class="sxs-lookup"><span data-stu-id="c0e44-217">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="c0e44-218">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros</span><span class="sxs-lookup"><span data-stu-id="c0e44-218">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="c0e44-219">Configure a identidade com um provedor de logon de terceiros.</span><span class="sxs-lookup"><span data-stu-id="c0e44-219">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="c0e44-220">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="c0e44-220">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="c0e44-221">Quando um usuário faz logon, a identidade coleta tokens de acesso e de atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="c0e44-221">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="c0e44-222">Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="c0e44-222">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="c0e44-223">Usar um token de acesso do servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="c0e44-223">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="c0e44-224">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="c0e44-224">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="c0e44-225">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente da identidade no cliente.</span><span class="sxs-lookup"><span data-stu-id="c0e44-225">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="c0e44-226">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="c0e44-226">We don't recommend this approach.</span></span> <span data-ttu-id="c0e44-227">Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="c0e44-227">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="c0e44-228">Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="c0e44-228">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="c0e44-229">Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="c0e44-229">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="c0e44-230">O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="c0e44-230">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="c0e44-231">Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.</span><span class="sxs-lookup"><span data-stu-id="c0e44-231">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="c0e44-232">Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="c0e44-232">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="c0e44-233">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="c0e44-233">Make an API call from the client to the server API.</span></span> <span data-ttu-id="c0e44-234">No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="c0e44-234">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="c0e44-235">Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="c0e44-235">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="c0e44-236">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="c0e44-236">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="c0e44-237">O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="c0e44-237">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
