---
title: BlazorCenários de segurança adicionais do ASP.NET Core Server
author: guardrex
description: Saiba como configurar o Blazor servidor para cenários de segurança adicionais.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 9d26cde4d8964a8285241bb0158d8e6f8d5f8dbc
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444068"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="2f2d8-103">BlazorCenários de segurança adicionais do ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="2f2d8-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="2f2d8-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="2f2d8-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="2f2d8-105">Passar tokens para um Blazor aplicativo de servidor</span><span class="sxs-lookup"><span data-stu-id="2f2d8-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="2f2d8-106">Os tokens disponíveis fora dos Razor componentes em um Blazor aplicativo de servidor podem ser passados para componentes com a abordagem descrita nesta seção.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="2f2d8-107">Para obter o código de exemplo, incluindo um `Startup.ConfigureServices` exemplo completo, consulte os [tokens de passagem para um Blazor aplicativo do lado do servidor](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="2f2d8-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="2f2d8-108">Autentique o Blazor aplicativo de servidor como você faria com Razor páginas regulares ou um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="2f2d8-109">Provisione e salve os tokens no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="2f2d8-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="2f2d8-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="2f2d8-111">Defina uma classe para passar o estado inicial do aplicativo com os tokens de acesso e de atualização:</span><span class="sxs-lookup"><span data-stu-id="2f2d8-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="2f2d8-112">Defina um serviço de provedor de token com **escopo** que possa ser usado no Blazor aplicativo para resolver os tokens da [injeção de dependência (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="2f2d8-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="2f2d8-113">No `Startup.ConfigureServices` , adicione serviços para:</span><span class="sxs-lookup"><span data-stu-id="2f2d8-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="2f2d8-114">No arquivo *_Host. cshtml* , crie e instância do `InitialApplicationState` e passe-o como um parâmetro para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2f2d8-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="2f2d8-115">No `App` componente (*app. Razor*), resolva o serviço e inicialize-o com os dados do parâmetro:</span><span class="sxs-lookup"><span data-stu-id="2f2d8-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="2f2d8-116">No serviço que faz uma solicitação de API segura, insira o provedor de token e recupere o token para chamar a API:</span><span class="sxs-lookup"><span data-stu-id="2f2d8-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="2f2d8-117">Usar pontos de extremidade do OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="2f2d8-117">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="2f2d8-118">A biblioteca de autenticação e os Blazor modelos usam pontos de extremidade do OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-118">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="2f2d8-119">Para usar um ponto de extremidade v 2.0, configure a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opção no <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="2f2d8-119">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="2f2d8-120">Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="2f2d8-120">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="2f2d8-121">Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a `Authority` propriedade diretamente.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-121">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="2f2d8-122">Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ou no arquivo de configurações do aplicativo com a `Authority` chave.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-122">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the `Authority` key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="2f2d8-123">Alterações de código</span><span class="sxs-lookup"><span data-stu-id="2f2d8-123">Code changes</span></span>

* <span data-ttu-id="2f2d8-124">A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-124">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="2f2d8-125">Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="2f2d8-125">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="2f2d8-126">na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-126">in the Azure documentation.</span></span>
* <span data-ttu-id="2f2d8-127">Como os recursos são especificados em URIs de escopo para pontos de extremidade v 2.0, remova a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> configuração de propriedade em <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="2f2d8-127">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="2f2d8-128">Você pode encontrar o URI da ID do aplicativo a ser usado na descrição do registro do aplicativo do provedor OIDC.</span><span class="sxs-lookup"><span data-stu-id="2f2d8-128">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
