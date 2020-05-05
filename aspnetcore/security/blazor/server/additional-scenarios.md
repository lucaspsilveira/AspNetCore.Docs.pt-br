---
title: Cenários Blazor de segurança adicionais do ASP.NET Core Server
author: guardrex
description: Saiba como configurar Blazor o servidor para cenários de segurança adicionais.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772798"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="806f6-103">Cenários Blazor de segurança adicionais do ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="806f6-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="806f6-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="806f6-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="806f6-105">Passar tokens para Blazor um aplicativo de servidor</span><span class="sxs-lookup"><span data-stu-id="806f6-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="806f6-106">Os tokens disponíveis fora Razor dos componentes em Blazor um aplicativo de servidor podem ser passados para componentes com a abordagem descrita nesta seção.</span><span class="sxs-lookup"><span data-stu-id="806f6-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="806f6-107">Para obter o código de exemplo, `Startup.ConfigureServices` incluindo um exemplo completo, consulte os [tokens de passagem Blazor para um aplicativo do lado do servidor](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="806f6-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="806f6-108">Autentique o Blazor aplicativo de servidor como você faria com páginas Razor regulares ou um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="806f6-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="806f6-109">Provisione e salve os tokens no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="806f6-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="806f6-110">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="806f6-110">For example:</span></span>

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

<span data-ttu-id="806f6-111">Defina uma classe para passar o estado inicial do aplicativo com os tokens de acesso e de atualização:</span><span class="sxs-lookup"><span data-stu-id="806f6-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="806f6-112">Defina um serviço de provedor de token com **escopo** que possa ser usado Blazor no aplicativo para resolver os tokens da [injeção de dependência (di)](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="806f6-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="806f6-113">No `Startup.ConfigureServices`, adicione serviços para:</span><span class="sxs-lookup"><span data-stu-id="806f6-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="806f6-114">No arquivo *_Host. cshtml* , crie e instância do `InitialApplicationState` e passe-o como um parâmetro para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="806f6-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="806f6-115">No `App` componente (*app. Razor*), resolva o serviço e inicialize-o com os dados do parâmetro:</span><span class="sxs-lookup"><span data-stu-id="806f6-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="806f6-116">No serviço que faz uma solicitação de API segura, insira o provedor de token e recupere o token para chamar a API:</span><span class="sxs-lookup"><span data-stu-id="806f6-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
