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
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206385"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Cenários Blazor de segurança adicionais do ASP.NET Core Server

Por [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Passar tokens para Blazor um aplicativo de servidor

Os tokens disponíveis fora dos componentes do Razor Blazor em um aplicativo de servidor podem ser passados para componentes com a abordagem descrita nesta seção. Para obter o código de exemplo, `Startup.ConfigureServices` incluindo um exemplo completo, consulte os [tokens de passagem Blazor para um aplicativo do lado do servidor](https://github.com/javiercn/blazor-server-aad-sample).

Autentique o Blazor aplicativo de servidor como você faria com um aplicativo normal de Razor pages ou MVC. Provisione e salve os tokens no cookie de autenticação. Por exemplo:

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

Defina uma classe para passar o estado inicial do aplicativo com os tokens de acesso e de atualização:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Defina um serviço de provedor de token com **escopo** que possa ser usado Blazor no aplicativo para resolver os tokens da [injeção de dependência (di)](xref:blazor/dependency-injection):

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

No `Startup.ConfigureServices`, adicione serviços para:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

No arquivo *_Host. cshtml* , crie e instância do `InitialApplicationState` e passe-o como um parâmetro para o aplicativo:

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

No `App` componente (*app. Razor*), resolva o serviço e inicialize-o com os dados do parâmetro:

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

No serviço que faz uma solicitação de API segura, insira o provedor de token e recupere o token para chamar a API:

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
