---
title: Aplicativos do Blazor Secure ASP.NET Core Server
author: guardrex
description: Saiba como mitigar ameaças Blazor de segurança aos aplicativos do Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626020"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="26da9-103">Aplicativos Secure ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="26da9-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="26da9-104">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="26da9-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="26da9-105">Os aplicativos Blazor Server adotam um modelo de processamento de dados *imponente,* onde o servidor e o cliente mantêm um relacionamento duradouro.</span><span class="sxs-lookup"><span data-stu-id="26da9-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="26da9-106">O estado persistente é mantido por um [circuito,](xref:blazor/state-management)que pode abranger conexões que também são potencialmente de longa duração.</span><span class="sxs-lookup"><span data-stu-id="26da9-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="26da9-107">Quando um usuário visita um site do Blazor Server, o servidor cria um circuito na memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="26da9-108">O circuito indica ao navegador qual conteúdo renderizar e responder a eventos, como quando o usuário seleciona um botão na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="26da9-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="26da9-109">Para executar essas ações, um circuito invoca funções JavaScript no navegador do usuário e nos métodos .NET no servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="26da9-110">Esta interação baseada em JavaScript bidirecional é referida como [JavaScript interop (Interop JS)](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="26da9-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="26da9-111">Como o JS interop ocorre pela Internet e o cliente usa um navegador remoto, os aplicativos do Blazor Server compartilham a maioria das preocupações de segurança do aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="26da9-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="26da9-112">Este tópico descreve ameaças comuns aos aplicativos do Blazor Server e fornece orientação de mitigação de ameaças focada em aplicativos voltados para a Internet.</span><span class="sxs-lookup"><span data-stu-id="26da9-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="26da9-113">Em ambientes restritos, como dentro de redes corporativas ou intranets, algumas das orientações de mitigação também:</span><span class="sxs-lookup"><span data-stu-id="26da9-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="26da9-114">Não se aplica no ambiente restrito.</span><span class="sxs-lookup"><span data-stu-id="26da9-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="26da9-115">Não vale o custo para implementar porque o risco de segurança é baixo em um ambiente restrito.</span><span class="sxs-lookup"><span data-stu-id="26da9-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="26da9-116">Modelo de projeto do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="26da9-116">Blazor Server project template</span></span>

<span data-ttu-id="26da9-117">O modelo de projeto Blazor Server pode ser configurado para autenticação quando o projeto for criado.</span><span class="sxs-lookup"><span data-stu-id="26da9-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26da9-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26da9-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26da9-119">Siga a orientação <xref:blazor/get-started> do Visual Studio no artigo para criar um novo projeto Blazor Server com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="26da9-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="26da9-120">Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="26da9-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="26da9-121">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="26da9-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="26da9-122">**Sem autenticação**</span><span class="sxs-lookup"><span data-stu-id="26da9-122">**No Authentication**</span></span>
* <span data-ttu-id="26da9-123">**Contas de usuário individuais** &ndash; as contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="26da9-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="26da9-124">Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26da9-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="26da9-125">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="26da9-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="26da9-126">**Contas de trabalho ou escola**</span><span class="sxs-lookup"><span data-stu-id="26da9-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="26da9-127">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="26da9-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26da9-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26da9-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="26da9-129">Siga a orientação do <xref:blazor/get-started> Visual Studio Code no artigo para criar um novo projeto blazor server com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="26da9-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="26da9-130">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="26da9-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="26da9-131">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="26da9-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="26da9-132">`{AUTHENTICATION}` valor</span><span class="sxs-lookup"><span data-stu-id="26da9-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="26da9-133">Sem Autenticação</span><span class="sxs-lookup"><span data-stu-id="26da9-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="26da9-134">Individual</span><span class="sxs-lookup"><span data-stu-id="26da9-134">Individual</span></span><br><span data-ttu-id="26da9-135">Usuários armazenados no aplicativo com o ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="26da9-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="26da9-136">Individual</span><span class="sxs-lookup"><span data-stu-id="26da9-136">Individual</span></span><br><span data-ttu-id="26da9-137">Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="26da9-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="26da9-138">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="26da9-138">Work or School Accounts</span></span><br><span data-ttu-id="26da9-139">Autenticação organizacional para um único locatário.</span><span class="sxs-lookup"><span data-stu-id="26da9-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="26da9-140">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="26da9-140">Work or School Accounts</span></span><br><span data-ttu-id="26da9-141">Autenticação organizacional para vários locatários.</span><span class="sxs-lookup"><span data-stu-id="26da9-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="26da9-142">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="26da9-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="26da9-143">O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="26da9-144">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="26da9-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26da9-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26da9-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26da9-146">Siga o Visual Studio para <xref:blazor/get-started> obter orientação do Mac no artigo.</span><span class="sxs-lookup"><span data-stu-id="26da9-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="26da9-147">Na **configuração da nova etapa do Aplicativo do Servidor Blazor,** selecione **Autenticação Individual (no aplicativo)** a partir da **queda de autenticação.**</span><span class="sxs-lookup"><span data-stu-id="26da9-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="26da9-148">O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Identidade Central.</span><span class="sxs-lookup"><span data-stu-id="26da9-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26da9-149">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="26da9-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26da9-150">Siga a orientação da <xref:blazor/get-started> .NET Core CLI no artigo para criar um novo projeto do Blazor Server com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="26da9-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="26da9-151">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="26da9-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="26da9-152">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="26da9-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="26da9-153">`{AUTHENTICATION}` valor</span><span class="sxs-lookup"><span data-stu-id="26da9-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="26da9-154">Sem Autenticação</span><span class="sxs-lookup"><span data-stu-id="26da9-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="26da9-155">Individual</span><span class="sxs-lookup"><span data-stu-id="26da9-155">Individual</span></span><br><span data-ttu-id="26da9-156">Usuários armazenados no aplicativo com o ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="26da9-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="26da9-157">Individual</span><span class="sxs-lookup"><span data-stu-id="26da9-157">Individual</span></span><br><span data-ttu-id="26da9-158">Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="26da9-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="26da9-159">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="26da9-159">Work or School Accounts</span></span><br><span data-ttu-id="26da9-160">Autenticação organizacional para um único locatário.</span><span class="sxs-lookup"><span data-stu-id="26da9-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="26da9-161">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="26da9-161">Work or School Accounts</span></span><br><span data-ttu-id="26da9-162">Autenticação organizacional para vários locatários.</span><span class="sxs-lookup"><span data-stu-id="26da9-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="26da9-163">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="26da9-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="26da9-164">O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="26da9-165">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="26da9-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="26da9-166">Passe tokens para um aplicativo Blazor Server</span><span class="sxs-lookup"><span data-stu-id="26da9-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="26da9-167">Auteire o aplicativo Blazor Server como você faria com um aplicativo regular razor pages ou MVC.</span><span class="sxs-lookup"><span data-stu-id="26da9-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="26da9-168">Proprovisionar e salvar os tokens no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="26da9-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="26da9-169">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="26da9-169">For example:</span></span>

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

<span data-ttu-id="26da9-170">Para obter o código `Startup.ConfigureServices` de exemplo, incluindo um exemplo completo, consulte os [tokens Passing para um aplicativo Blazor do lado](https://github.com/javiercn/blazor-server-aad-sample)do servidor .</span><span class="sxs-lookup"><span data-stu-id="26da9-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="26da9-171">Defina uma classe para passar no estado inicial do aplicativo com os tokens de acesso e atualização:</span><span class="sxs-lookup"><span data-stu-id="26da9-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="26da9-172">Defina um serviço de provedor de token **escopo** que pode ser usado dentro do aplicativo Blazor para resolver os tokens de DI:</span><span class="sxs-lookup"><span data-stu-id="26da9-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="26da9-173">In, `Startup.ConfigureServices`adicionar serviços para:</span><span class="sxs-lookup"><span data-stu-id="26da9-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="26da9-174">No arquivo *_Host.cshtml,* crie `InitialApplicationState` e aprove e passe-o como parâmetro para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="26da9-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="26da9-175">No `App` componente *(App.razor),* resolva o serviço e inicialize-o com os dados do parâmetro:</span><span class="sxs-lookup"><span data-stu-id="26da9-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="26da9-176">No serviço que faz uma solicitação de API segura, injete o provedor de tokens e recupere o token para chamar a API:</span><span class="sxs-lookup"><span data-stu-id="26da9-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="resource-exhaustion"></a><span data-ttu-id="26da9-177">Esgotamento de recursos</span><span class="sxs-lookup"><span data-stu-id="26da9-177">Resource exhaustion</span></span>

<span data-ttu-id="26da9-178">O esgotamento dos recursos pode ocorrer quando um cliente interage com o servidor e faz com que o servidor consuma recursos excessivos.</span><span class="sxs-lookup"><span data-stu-id="26da9-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="26da9-179">O consumo excessivo de recursos afeta principalmente:</span><span class="sxs-lookup"><span data-stu-id="26da9-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="26da9-180">Cpu</span><span class="sxs-lookup"><span data-stu-id="26da9-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="26da9-181">Memória</span><span class="sxs-lookup"><span data-stu-id="26da9-181">Memory</span></span>](#memory)
* [<span data-ttu-id="26da9-182">Conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="26da9-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="26da9-183">Ataques de negação de serviço (DoS) geralmente buscam esgotar os recursos de um aplicativo ou servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="26da9-184">No entanto, o esgotamento dos recursos não é necessariamente o resultado de um ataque ao sistema.</span><span class="sxs-lookup"><span data-stu-id="26da9-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="26da9-185">Por exemplo, recursos finitos podem ser esgotados devido à alta demanda do usuário.</span><span class="sxs-lookup"><span data-stu-id="26da9-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="26da9-186">O DoS é coberto ainda mais na seção [de ataques Negação de serviço (DoS).](#denial-of-service-dos-attacks)</span><span class="sxs-lookup"><span data-stu-id="26da9-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="26da9-187">Recursos externos à estrutura de Blazor, como bancos de dados e alças de arquivos (usados para ler e gravar arquivos), também podem sofrer esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="26da9-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="26da9-188">Para obter mais informações, consulte <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="26da9-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="26da9-189">CPU</span><span class="sxs-lookup"><span data-stu-id="26da9-189">CPU</span></span>

<span data-ttu-id="26da9-190">A exaustão da CPU pode ocorrer quando um ou mais clientes forçam o servidor a realizar um trabalho intensivo da CPU.</span><span class="sxs-lookup"><span data-stu-id="26da9-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="26da9-191">Por exemplo, considere um aplicativo Blazor Server que calcula um *número Fibonnacci*.</span><span class="sxs-lookup"><span data-stu-id="26da9-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="26da9-192">Um número de Fibonnacci é produzido a partir de uma seqüência de Fibonnacci, onde cada número na seqüência é a soma dos dois números anteriores.</span><span class="sxs-lookup"><span data-stu-id="26da9-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="26da9-193">A quantidade de trabalho necessária para alcançar a resposta depende do comprimento da seqüência e do tamanho do valor inicial.</span><span class="sxs-lookup"><span data-stu-id="26da9-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="26da9-194">Se o aplicativo não colocar limites à solicitação de um cliente, os cálculos intensivos em CPU podem dominar o tempo da CPU e diminuir o desempenho de outras tarefas.</span><span class="sxs-lookup"><span data-stu-id="26da9-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="26da9-195">O consumo excessivo de recursos é uma preocupação de segurança que afeta a disponibilidade.</span><span class="sxs-lookup"><span data-stu-id="26da9-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="26da9-196">A exaustão da CPU é uma preocupação para todos os aplicativos voltados para o público.</span><span class="sxs-lookup"><span data-stu-id="26da9-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="26da9-197">Em aplicativos web regulares, solicitações e conexões são uma salvaguarda, mas os aplicativos do Blazor Server não fornecem as mesmas salvaguardas.</span><span class="sxs-lookup"><span data-stu-id="26da9-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="26da9-198">Os aplicativos do Blazor Server devem incluir verificações e limites apropriados antes de realizar um trabalho potencialmente intensivo em CPU.</span><span class="sxs-lookup"><span data-stu-id="26da9-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="26da9-199">Memória</span><span class="sxs-lookup"><span data-stu-id="26da9-199">Memory</span></span>

<span data-ttu-id="26da9-200">A exaustão da memória pode ocorrer quando um ou mais clientes forçam o servidor a consumir uma grande quantidade de memória.</span><span class="sxs-lookup"><span data-stu-id="26da9-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="26da9-201">Por exemplo, considere um aplicativo lateral do servidor Blazor com um componente que aceita e exibe uma lista de itens.</span><span class="sxs-lookup"><span data-stu-id="26da9-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="26da9-202">Se o aplicativo Blazor não colocar limites ao número de itens permitidos ou ao número de itens entregues ao cliente, o processamento e renderização com uso intensivo de memória podem dominar a memória do servidor até o ponto em que o desempenho do servidor sofre.</span><span class="sxs-lookup"><span data-stu-id="26da9-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="26da9-203">O servidor pode travar ou diminuir até o ponto em que parece ter caído.</span><span class="sxs-lookup"><span data-stu-id="26da9-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="26da9-204">Considere o seguinte cenário para manter e exibir uma lista de itens que dizem respeito a um potencial cenário de exaustão de memória no servidor:</span><span class="sxs-lookup"><span data-stu-id="26da9-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="26da9-205">Os itens em `List<MyItem>` uma propriedade ou campo usam a memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="26da9-206">Se o aplicativo permitir que a lista de itens cresça sem limites, há o risco de o servidor ficar sem memória.</span><span class="sxs-lookup"><span data-stu-id="26da9-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="26da9-207">Ficar sem memória faz com que a sessão atual termine (falha) e todas as sessões simultâneas nessa instância do servidor recebam uma exceção fora da memória.</span><span class="sxs-lookup"><span data-stu-id="26da9-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="26da9-208">Para evitar que esse cenário ocorra, o aplicativo deve usar uma estrutura de dados que imponha um limite de itens para usuários simultâneos.</span><span class="sxs-lookup"><span data-stu-id="26da9-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="26da9-209">Se um esquema de paginação não for usado para renderização, o servidor usará memória adicional para objetos que não são visíveis na ia.</span><span class="sxs-lookup"><span data-stu-id="26da9-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="26da9-210">Sem um limite no número de itens, as demandas de memória podem esgotar a memória disponível do servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="26da9-211">Para evitar esse cenário, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="26da9-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="26da9-212">Use listas paginadas ao renderizar.</span><span class="sxs-lookup"><span data-stu-id="26da9-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="26da9-213">Apenas exiba os primeiros 100 a 1.000 itens e exija que o usuário insira critérios de pesquisa para encontrar itens além dos itens exibidos.</span><span class="sxs-lookup"><span data-stu-id="26da9-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="26da9-214">Para um cenário de renderização mais avançado, implemente listas ou grades que suportem a *virtualização*.</span><span class="sxs-lookup"><span data-stu-id="26da9-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="26da9-215">Usando a virtualização, as listas só renderizam um subconjunto de itens atualmente visíveis para o usuário.</span><span class="sxs-lookup"><span data-stu-id="26da9-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="26da9-216">Quando o usuário interage com a barra de rolagem na ui, o componente renderiza apenas os itens necessários para exibição.</span><span class="sxs-lookup"><span data-stu-id="26da9-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="26da9-217">Os itens que atualmente não são necessários para exibição podem ser mantidos no armazenamento secundário, que é a abordagem ideal.</span><span class="sxs-lookup"><span data-stu-id="26da9-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="26da9-218">Itens não exibidos também podem ser mantidos na memória, o que é menos ideal.</span><span class="sxs-lookup"><span data-stu-id="26da9-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="26da9-219">Os aplicativos Blazor Server oferecem um modelo de programação semelhante a outras estruturas de interface do usuário para aplicativos estatais, como WPF, Windows Forms ou Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="26da9-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="26da9-220">A principal diferença é que em vários dos frameworks de IA a memória consumida pelo aplicativo pertence ao cliente e afeta apenas esse cliente individual.</span><span class="sxs-lookup"><span data-stu-id="26da9-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="26da9-221">Por exemplo, um aplicativo Blazor WebAssembly é executado inteiramente no cliente e só usa recursos de memória do cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="26da9-222">No cenário do Blazor Server, a memória consumida pelo aplicativo pertence ao servidor e é compartilhada entre os clientes na instância do servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="26da9-223">As demandas de memória do lado do servidor são uma consideração para todos os aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="26da9-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="26da9-224">No entanto, a maioria dos aplicativos da Web são apátridas, e a memória usada durante o processamento de uma solicitação é liberada quando a resposta é retornada.</span><span class="sxs-lookup"><span data-stu-id="26da9-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="26da9-225">Como uma recomendação geral, não permita que os clientes aloquem uma quantidade de memória não vinculada como em qualquer outro aplicativo do lado do servidor que persista nas conexões com o cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="26da9-226">A memória consumida por um aplicativo Blazor Server persiste por mais tempo do que uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="26da9-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="26da9-227">Durante o desenvolvimento, um profiler pode ser usado ou um rastreamento capturado para avaliar as demandas de memória dos clientes.</span><span class="sxs-lookup"><span data-stu-id="26da9-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="26da9-228">Um profiler ou rastreamento não capturará a memória alocada a um cliente específico.</span><span class="sxs-lookup"><span data-stu-id="26da9-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="26da9-229">Para capturar o uso de memória de um cliente específico durante o desenvolvimento, capture um dump e examine a demanda de memória de todos os objetos enraizados no circuito de um usuário.</span><span class="sxs-lookup"><span data-stu-id="26da9-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="26da9-230">Conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="26da9-230">Client connections</span></span>

<span data-ttu-id="26da9-231">O esgotamento da conexão pode ocorrer quando um ou mais clientes abrem muitas conexões simultâneas ao servidor, impedindo que outros clientes estabeleçam novas conexões.</span><span class="sxs-lookup"><span data-stu-id="26da9-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="26da9-232">Os clientes Blazor estabelecem uma única conexão por sessão e mantêm a conexão aberta enquanto a janela do navegador estiver aberta.</span><span class="sxs-lookup"><span data-stu-id="26da9-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="26da9-233">As exigências do servidor de manter todas as conexões não são específicas dos aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="26da9-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="26da9-234">Dada a natureza persistente das conexões e a natureza imponente dos aplicativos Blazor Server, a exaustão da conexão é um risco maior para a disponibilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="26da9-235">Por padrão, não há limite no número de conexões por usuário para um aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="26da9-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="26da9-236">Se o aplicativo precisar de um limite de conexão, faça uma ou mais das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="26da9-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="26da9-237">Requerem autenticação, o que naturalmente limita a capacidade dos usuários não autorizados de se conectarem ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="26da9-238">Para que esse cenário seja eficaz, os usuários devem ser impedidos de provisionar novos usuários à vontade.</span><span class="sxs-lookup"><span data-stu-id="26da9-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="26da9-239">Limitar o número de conexões por usuário.</span><span class="sxs-lookup"><span data-stu-id="26da9-239">Limit the number of connections per user.</span></span> <span data-ttu-id="26da9-240">A limitação de conexões pode ser realizada através das seguintes abordagens.</span><span class="sxs-lookup"><span data-stu-id="26da9-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="26da9-241">Tenha cuidado para permitir que usuários legítimos acessem o aplicativo (por exemplo, quando um limite de conexão é estabelecido com base no endereço IP do cliente).</span><span class="sxs-lookup"><span data-stu-id="26da9-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="26da9-242">Ao nível do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="26da9-242">At the app level:</span></span>
    * <span data-ttu-id="26da9-243">Extensibilidade de roteamento de ponto final.</span><span class="sxs-lookup"><span data-stu-id="26da9-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="26da9-244">Exija autenticação para se conectar ao aplicativo e acompanhar as sessões ativas por usuário.</span><span class="sxs-lookup"><span data-stu-id="26da9-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="26da9-245">Rejeitar novas sessões ao atingir um limite.</span><span class="sxs-lookup"><span data-stu-id="26da9-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="26da9-246">As conexões do Proxy WebSocket a um aplicativo através do uso de um proxy, como o [Azure SignalR Service,](/azure/azure-signalr/signalr-overview) que multiplexa conexões de clientes para um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="26da9-247">Isso fornece um aplicativo com maior capacidade de conexão do que um único cliente pode estabelecer, impedindo que um cliente esgote as conexões com o servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="26da9-248">No nível do servidor: Use um proxy/gateway na frente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="26da9-249">Por exemplo, [o Azure Front Door](/azure/frontdoor/front-door-overview) permite que você defina, gerencie e monitore o roteamento global do tráfego da Web para um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="26da9-250">Ataques de negação de serviço (DoS)</span><span class="sxs-lookup"><span data-stu-id="26da9-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="26da9-251">Os ataques de Negação de Serviço (DoS) envolvem um cliente fazendo com que o servidor esgote um ou mais de seus recursos tornando o aplicativo indisponível.</span><span class="sxs-lookup"><span data-stu-id="26da9-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="26da9-252">Os aplicativos do Blazor Server incluem alguns limites padrão e dependem de outros limites ASP.NET Core e SignalR para proteger contra ataques DoS:</span><span class="sxs-lookup"><span data-stu-id="26da9-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="26da9-253">Limite do aplicativo Blazor Server</span><span class="sxs-lookup"><span data-stu-id="26da9-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="26da9-254">Descrição</span><span class="sxs-lookup"><span data-stu-id="26da9-254">Description</span></span> | <span data-ttu-id="26da9-255">Padrão</span><span class="sxs-lookup"><span data-stu-id="26da9-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="26da9-256">Número máximo de circuitos desconectados que um determinado servidor mantém na memória de cada vez.</span><span class="sxs-lookup"><span data-stu-id="26da9-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="26da9-257">100</span><span class="sxs-lookup"><span data-stu-id="26da9-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="26da9-258">A quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser derrubado.</span><span class="sxs-lookup"><span data-stu-id="26da9-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="26da9-259">3 minutos</span><span class="sxs-lookup"><span data-stu-id="26da9-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="26da9-260">A quantidade máxima de tempo que o servidor espera antes de cronometrar uma invocação de função JavaScript assíncrona.</span><span class="sxs-lookup"><span data-stu-id="26da9-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="26da9-261">1 minuto</span><span class="sxs-lookup"><span data-stu-id="26da9-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="26da9-262">Número máximo de lotes de renderização não reconhecidos que o servidor mantém na memória por circuito em um dado momento para suportar uma reconexão robusta.</span><span class="sxs-lookup"><span data-stu-id="26da9-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="26da9-263">Depois de atingir o limite, o servidor deixa de produzir novos lotes de renderização até que um ou mais lotes tenham sido reconhecidos pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="26da9-264">10</span><span class="sxs-lookup"><span data-stu-id="26da9-264">10</span></span> |


| <span data-ttu-id="26da9-265">Limite signalR e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26da9-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="26da9-266">Descrição</span><span class="sxs-lookup"><span data-stu-id="26da9-266">Description</span></span> | <span data-ttu-id="26da9-267">Padrão</span><span class="sxs-lookup"><span data-stu-id="26da9-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="26da9-268">Tamanho da mensagem para uma mensagem individual.</span><span class="sxs-lookup"><span data-stu-id="26da9-268">Message size for an individual message.</span></span> | <span data-ttu-id="26da9-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="26da9-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="26da9-270">Interações com o navegador (cliente)</span><span class="sxs-lookup"><span data-stu-id="26da9-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="26da9-271">Um cliente interage com o servidor através do js interop event dispatching and render completing.</span><span class="sxs-lookup"><span data-stu-id="26da9-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="26da9-272">A comunicação interop JS vai para os dois lados entre JavaScript e .NET:</span><span class="sxs-lookup"><span data-stu-id="26da9-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="26da9-273">Os eventos do navegador são enviados do cliente para o servidor de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="26da9-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="26da9-274">O servidor responde de forma assíncrona rerenderizando a ui conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="26da9-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="26da9-275">Funções JavaScript invocadas a partir de .NET</span><span class="sxs-lookup"><span data-stu-id="26da9-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="26da9-276">Para chamadas dos métodos .NET para JavaScript:</span><span class="sxs-lookup"><span data-stu-id="26da9-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="26da9-277">Todas as invocações têm um tempo de intervalo configurável após o qual falham, retornando a <xref:System.OperationCanceledException> ao chamador.</span><span class="sxs-lookup"><span data-stu-id="26da9-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="26da9-278">Há um tempo padrão para as`CircuitOptions.JSInteropDefaultCallTimeout`chamadas de um minuto.</span><span class="sxs-lookup"><span data-stu-id="26da9-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="26da9-279">Para configurar esse limite, consulte <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span><span class="sxs-lookup"><span data-stu-id="26da9-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="26da9-280">Um token de cancelamento pode ser fornecido para controlar o cancelamento por chamada.</span><span class="sxs-lookup"><span data-stu-id="26da9-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="26da9-281">Confie no tempo limite de chamada padrão, sempre que possível e limite de tempo, qualquer chamada ao cliente se um token de cancelamento for fornecido.</span><span class="sxs-lookup"><span data-stu-id="26da9-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="26da9-282">O resultado de uma chamada JavaScript não é confiável.</span><span class="sxs-lookup"><span data-stu-id="26da9-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="26da9-283">O Blazor cliente do aplicativo em execução no navegador procura a função JavaScript para invocar.</span><span class="sxs-lookup"><span data-stu-id="26da9-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="26da9-284">A função é invocada, e ou o resultado ou um erro é produzido.</span><span class="sxs-lookup"><span data-stu-id="26da9-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="26da9-285">Um cliente mal-intencionado pode tentar:</span><span class="sxs-lookup"><span data-stu-id="26da9-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="26da9-286">Causar um problema no aplicativo retornando um erro da função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26da9-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="26da9-287">Induzir um comportamento não intencional no servidor retornando um resultado inesperado da função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26da9-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="26da9-288">Tome as seguintes precauções para se proteger dos cenários anteriores:</span><span class="sxs-lookup"><span data-stu-id="26da9-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="26da9-289">Enrole as chamadas de interop js dentro de demonstrações [de try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para explicar erros que podem ocorrer durante as invocações.</span><span class="sxs-lookup"><span data-stu-id="26da9-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="26da9-290">Para obter mais informações, consulte <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="26da9-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="26da9-291">Valide os dados retornados das invocações de interop JS, incluindo mensagens de erro, antes de tomar qualquer ação.</span><span class="sxs-lookup"><span data-stu-id="26da9-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="26da9-292">Métodos .NET invocados a partir do navegador</span><span class="sxs-lookup"><span data-stu-id="26da9-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="26da9-293">Não confie em chamadas de JavaScript para métodos .NET.</span><span class="sxs-lookup"><span data-stu-id="26da9-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="26da9-294">Quando um método .NET for exposto ao JavaScript, considere como o método .NET é invocado:</span><span class="sxs-lookup"><span data-stu-id="26da9-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="26da9-295">Trate qualquer método .NET exposto ao JavaScript como você seria um ponto final público para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="26da9-296">Validar a entrada.</span><span class="sxs-lookup"><span data-stu-id="26da9-296">Validate input.</span></span>
    * <span data-ttu-id="26da9-297">Certifique-se de que os valores estão dentro dos intervalos esperados.</span><span class="sxs-lookup"><span data-stu-id="26da9-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="26da9-298">Certifique-se de que o usuário tem permissão para executar a ação solicitada.</span><span class="sxs-lookup"><span data-stu-id="26da9-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="26da9-299">Não aloque uma quantidade excessiva de recursos como parte da invocação do método .NET.</span><span class="sxs-lookup"><span data-stu-id="26da9-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="26da9-300">Por exemplo, realize verificações e coloque limites no uso da CPU e da memória.</span><span class="sxs-lookup"><span data-stu-id="26da9-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="26da9-301">Leve em conta que métodos estáticos e de instância podem ser expostos a clientes JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26da9-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="26da9-302">Evite compartilhar o estado entre as sessões, a menos que o projeto exija o compartilhamento de estado com restrições apropriadas.</span><span class="sxs-lookup"><span data-stu-id="26da9-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="26da9-303">Por exemplo, métodos `DotNetReference` expostos através de objetos que são originalmente criados através de injeção de dependência (DI), os objetos devem ser registrados como objetos escopo.</span><span class="sxs-lookup"><span data-stu-id="26da9-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="26da9-304">Isso se aplica a qualquer Blazor serviço DI que o aplicativo Server usa.</span><span class="sxs-lookup"><span data-stu-id="26da9-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="26da9-305">Para métodos estáticos, evite estabelecer um estado que não possa ser escopo para o cliente, a menos que o aplicativo esteja compartilhando explicitamente estado por design em todos os usuários em uma instância de servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="26da9-306">Evite passar dados fornecidos pelo usuário em parâmetros para chamadas JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26da9-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="26da9-307">Se a passagem de dados em parâmetros for absolutamente necessária, certifique-se de que o código JavaScript lida com a passagem dos dados sem introduzir vulnerabilidades [de scripting entre sites (XSS).](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="26da9-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="26da9-308">Por exemplo, não escreva dados fornecidos pelo usuário no DoM `innerHTML` (Document Object Model, modelo de objeto de documento) definindo a propriedade de um elemento.</span><span class="sxs-lookup"><span data-stu-id="26da9-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="26da9-309">Considere usar [a Política de Segurança de Conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para desativar `eval` e outros primitivos JavaScript inseguros.</span><span class="sxs-lookup"><span data-stu-id="26da9-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="26da9-310">Evite implementar o envio personalizado de invocações .NET em cima da implementação de expedição do framework.</span><span class="sxs-lookup"><span data-stu-id="26da9-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="26da9-311">Expor os métodos .NET ao navegador é um cenário Blazor avançado, não recomendado para o desenvolvimento geral.</span><span class="sxs-lookup"><span data-stu-id="26da9-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="26da9-312">Eventos</span><span class="sxs-lookup"><span data-stu-id="26da9-312">Events</span></span>

<span data-ttu-id="26da9-313">Os eventos fornecem um Blazor ponto de entrada para um aplicativo do Servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="26da9-314">As mesmas regras para salvaguardar pontos finais em Blazor aplicativos web se aplicam ao tratamento de eventos em aplicativos do Servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="26da9-315">Um cliente mal-intencionado pode enviar qualquer dado que desejar enviar como carga útil para um evento.</span><span class="sxs-lookup"><span data-stu-id="26da9-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="26da9-316">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="26da9-316">For example:</span></span>

* <span data-ttu-id="26da9-317">Um evento de `<select>` alteração para um poderia enviar um valor que não está dentro das opções que o aplicativo apresentou ao cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="26da9-318">Um `<input>` poderia enviar qualquer dado de texto para o servidor, ignorando a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="26da9-319">O aplicativo deve validar os dados para qualquer evento que o aplicativo manuseie.</span><span class="sxs-lookup"><span data-stu-id="26da9-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="26da9-320">Os Blazor [componentes de formulários-quadro](xref:blazor/forms-validation) realizam validações básicas.</span><span class="sxs-lookup"><span data-stu-id="26da9-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="26da9-321">Se o aplicativo usar componentes de formulários personalizados, o código personalizado deve ser escrito para validar os dados do evento conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="26da9-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="26da9-322">Os eventos do servidor são assíncronos, então vários eventos podem ser enviados para o servidor antes que o aplicativo tenha tempo de reagir produzindo uma nova renderização.</span><span class="sxs-lookup"><span data-stu-id="26da9-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="26da9-323">Isso tem algumas implicações de segurança a considerar.</span><span class="sxs-lookup"><span data-stu-id="26da9-323">This has some security implications to consider.</span></span> <span data-ttu-id="26da9-324">Limitar as ações do cliente no aplicativo deve ser realizado dentro dos manipuladores de eventos e não depender do estado de exibição renderizado atual.</span><span class="sxs-lookup"><span data-stu-id="26da9-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="26da9-325">Considere um componente de contador que deve permitir que um usuário incremente um contador no máximo três vezes.</span><span class="sxs-lookup"><span data-stu-id="26da9-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="26da9-326">O botão para incrementar o contador é `count`condicionalmente baseado no valor de :</span><span class="sxs-lookup"><span data-stu-id="26da9-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="26da9-327">Um cliente pode despachar um ou mais eventos de incremento antes que a estrutura produza uma nova renderização deste componente.</span><span class="sxs-lookup"><span data-stu-id="26da9-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="26da9-328">O resultado é `count` que o pode ser incrementado *mais de três vezes* pelo usuário porque o botão não é removido pela ui rapidamente o suficiente.</span><span class="sxs-lookup"><span data-stu-id="26da9-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="26da9-329">A maneira correta de atingir `count` o limite de três incrementos é mostrada no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="26da9-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="26da9-330">Adicionando `if (count < 3) { ... }` a verificação dentro do `count` manipulador, a decisão de incrementar é baseada no estado atual do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="26da9-331">A decisão não se baseia no estado da UI como era no exemplo anterior, que pode estar temporariamente obsoleto.</span><span class="sxs-lookup"><span data-stu-id="26da9-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="26da9-332">Proteja-se contra vários despachos</span><span class="sxs-lookup"><span data-stu-id="26da9-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="26da9-333">Se um retorno de chamada de evento invocar uma operação de longa duração de forma assíncrona, como buscar dados de um serviço externo ou banco de dados, considere usar um guarda.</span><span class="sxs-lookup"><span data-stu-id="26da9-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="26da9-334">O protetor pode impedir que o usuário enfileiravárias operações enquanto a operação está em andamento com feedback visual.</span><span class="sxs-lookup"><span data-stu-id="26da9-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="26da9-335">O código do `isLoading` `true` componente `GetForecastAsync` a seguir define-se enquanto obtém dados do servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="26da9-336">`isLoading` Enquanto `true`estiver, o botão está desativado na ui:</span><span class="sxs-lookup"><span data-stu-id="26da9-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="26da9-337">O padrão de proteção demonstrado no exemplo anterior funciona se a `async` - `await` operação de fundo for executada de forma assíncrona com o padrão.</span><span class="sxs-lookup"><span data-stu-id="26da9-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="26da9-338">Cancele cedo e evite usar após o descarte</span><span class="sxs-lookup"><span data-stu-id="26da9-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="26da9-339">Além de usar um guarda como descrito na seção [Desmembramento contra vários despachos,](#guard-against-multiple-dispatches) considere usar um <xref:System.Threading.CancellationToken> para cancelar operações de longa duração quando o componente for descartado.</span><span class="sxs-lookup"><span data-stu-id="26da9-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="26da9-340">Esta abordagem tem o benefício adicional de evitar *o uso após o descarte* em componentes:</span><span class="sxs-lookup"><span data-stu-id="26da9-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="26da9-341">Evite eventos que produzam grandes quantidades de dados</span><span class="sxs-lookup"><span data-stu-id="26da9-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="26da9-342">Alguns eventos do `oninput` DOM, como ou `onscroll`, podem produzir uma grande quantidade de dados.</span><span class="sxs-lookup"><span data-stu-id="26da9-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="26da9-343">Evite usar esses Blazor eventos em aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="26da9-344">Orientação adicional de segurança</span><span class="sxs-lookup"><span data-stu-id="26da9-344">Additional security guidance</span></span>

<span data-ttu-id="26da9-345">As orientações para a Blazor garantia de ASP.NET os aplicativos Core se aplicam aos aplicativos do Server e são abordados nas seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="26da9-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="26da9-346">Registro e dados confidenciais</span><span class="sxs-lookup"><span data-stu-id="26da9-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="26da9-347">Proteger informações em trânsito com HTTPS</span><span class="sxs-lookup"><span data-stu-id="26da9-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="26da9-348">[Scripting entre sites (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="26da9-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="26da9-349">Proteção de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="26da9-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="26da9-350">Click-jacking</span><span class="sxs-lookup"><span data-stu-id="26da9-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="26da9-351">Redirecionamentos abertos</span><span class="sxs-lookup"><span data-stu-id="26da9-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="26da9-352">Registro e dados confidenciais</span><span class="sxs-lookup"><span data-stu-id="26da9-352">Logging and sensitive data</span></span>

<span data-ttu-id="26da9-353">As interações de interop js entre o cliente e o <xref:Microsoft.Extensions.Logging.ILogger> servidor são registradas nos registros do servidor com instâncias.</span><span class="sxs-lookup"><span data-stu-id="26da9-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="26da9-354">evita registrar informações confidenciais, como eventos reais ou entradas e saídas interop JS.</span><span class="sxs-lookup"><span data-stu-id="26da9-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="26da9-355">Quando ocorre um erro no servidor, o framework notifica o cliente e destrói a sessão.</span><span class="sxs-lookup"><span data-stu-id="26da9-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="26da9-356">Por padrão, o cliente recebe uma mensagem de erro genérica que pode ser vista nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="26da9-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="26da9-357">O erro do lado do cliente não inclui o callstack e não fornece detalhes sobre a causa do erro, mas os registros do servidor contêm essas informações.</span><span class="sxs-lookup"><span data-stu-id="26da9-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="26da9-358">Para fins de desenvolvimento, informações confidenciais de erro podem ser disponibilizadas ao cliente, permitindo erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="26da9-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="26da9-359">Habilitar erros detalhados com:</span><span class="sxs-lookup"><span data-stu-id="26da9-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="26da9-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="26da9-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="26da9-361">`DetailedErrors`chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="26da9-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="26da9-362">Por exemplo, `ASPNETCORE_DETAILEDERRORS` defina a variável `true`de ambiente para um valor de .</span><span class="sxs-lookup"><span data-stu-id="26da9-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="26da9-363">Expor informações de erro aos clientes na Internet é um risco de segurança que deve ser sempre evitado.</span><span class="sxs-lookup"><span data-stu-id="26da9-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="26da9-364">Proteger informações em trânsito com HTTPS</span><span class="sxs-lookup"><span data-stu-id="26da9-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="26da9-365">O SignalR servidor usa para comunicação entre o cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="26da9-366">O servidor normalmente SignalR usa o transporte que negocia, que normalmente é websockets.</span><span class="sxs-lookup"><span data-stu-id="26da9-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="26da9-367">O servidor não garante a integridade e a confidencialidade dos dados enviados entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="26da9-368">Use sempre HTTPS.</span><span class="sxs-lookup"><span data-stu-id="26da9-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="26da9-369">Scripting entre sites (XSS)</span><span class="sxs-lookup"><span data-stu-id="26da9-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="26da9-370">O scripting entre sites (XSS) permite que uma parte não autorizada execute lógica arbitrária no contexto do navegador.</span><span class="sxs-lookup"><span data-stu-id="26da9-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="26da9-371">Um aplicativo comprometido pode potencialmente executar código arbitrário no cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="26da9-372">A vulnerabilidade pode ser usada para potencialmente executar uma série de ações maliciosas contra o servidor:</span><span class="sxs-lookup"><span data-stu-id="26da9-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="26da9-373">Despachar eventos falsos/inválidos para o servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="26da9-374">Falha de despacho/conclusão de renderização inválida.</span><span class="sxs-lookup"><span data-stu-id="26da9-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="26da9-375">Evite despachar as conclusões da renderização.</span><span class="sxs-lookup"><span data-stu-id="26da9-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="26da9-376">Despachar chamadas interop de JavaScript para .NET.</span><span class="sxs-lookup"><span data-stu-id="26da9-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="26da9-377">Modifique a resposta de chamadas interop de .NET para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26da9-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="26da9-378">Evite enviar resultados de interop .NET para JS.</span><span class="sxs-lookup"><span data-stu-id="26da9-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="26da9-379">A Blazor estrutura do servidor toma medidas para proteger contra algumas das ameaças anteriores:</span><span class="sxs-lookup"><span data-stu-id="26da9-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="26da9-380">Pare de produzir novas atualizações de iu se o cliente não estiver reconhecendo lotes de renderização.</span><span class="sxs-lookup"><span data-stu-id="26da9-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="26da9-381">Configurado `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`com .</span><span class="sxs-lookup"><span data-stu-id="26da9-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="26da9-382">Apagar qualquer chamada .NET para JavaScript após um minuto sem receber uma resposta do cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="26da9-383">Configurado `CircuitOptions.JSInteropDefaultCallTimeout`com .</span><span class="sxs-lookup"><span data-stu-id="26da9-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="26da9-384">Executa a validação básica em todas as entradas provenientes do navegador durante a interop JS:</span><span class="sxs-lookup"><span data-stu-id="26da9-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="26da9-385">As referências .NET são válidas e do tipo esperado pelo método .NET.</span><span class="sxs-lookup"><span data-stu-id="26da9-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="26da9-386">Os dados não são mal formados.</span><span class="sxs-lookup"><span data-stu-id="26da9-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="26da9-387">O número correto de argumentos para o método está presente na carga útil.</span><span class="sxs-lookup"><span data-stu-id="26da9-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="26da9-388">Os argumentos ou resultado podem ser desserializados corretamente antes de invocar o método.</span><span class="sxs-lookup"><span data-stu-id="26da9-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="26da9-389">Executa a validação básica em todas as entradas provenientes do navegador a partir de eventos despachados:</span><span class="sxs-lookup"><span data-stu-id="26da9-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="26da9-390">O evento tem um tipo válido.</span><span class="sxs-lookup"><span data-stu-id="26da9-390">The event has a valid type.</span></span>
  * <span data-ttu-id="26da9-391">Os dados do evento podem ser desserializados.</span><span class="sxs-lookup"><span data-stu-id="26da9-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="26da9-392">Há um manipulador de eventos associado ao evento.</span><span class="sxs-lookup"><span data-stu-id="26da9-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="26da9-393">Além das garantias que a estrutura implementa, o aplicativo deve ser codificado pelo desenvolvedor para proteger contra ameaças e tomar as medidas apropriadas:</span><span class="sxs-lookup"><span data-stu-id="26da9-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="26da9-394">Sempre valide dados ao lidar com eventos.</span><span class="sxs-lookup"><span data-stu-id="26da9-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="26da9-395">Tome as medidas apropriadas ao receber dados inválidos:</span><span class="sxs-lookup"><span data-stu-id="26da9-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="26da9-396">Ignore os dados e retorne.</span><span class="sxs-lookup"><span data-stu-id="26da9-396">Ignore the data and return.</span></span> <span data-ttu-id="26da9-397">Isso permite que o aplicativo continue processando solicitações.</span><span class="sxs-lookup"><span data-stu-id="26da9-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="26da9-398">Se o aplicativo determinar que a entrada é ilegítima e não pode ser produzida por cliente legítimo, lance uma exceção.</span><span class="sxs-lookup"><span data-stu-id="26da9-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="26da9-399">Uma exceção derruba o circuito e termina a sessão.</span><span class="sxs-lookup"><span data-stu-id="26da9-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="26da9-400">Não confie na mensagem de erro fornecida pelas conclusões do lote de renderização incluídas nos registros.</span><span class="sxs-lookup"><span data-stu-id="26da9-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="26da9-401">O erro é fornecido pelo cliente e geralmente não pode ser confiável, pois o cliente pode estar comprometido.</span><span class="sxs-lookup"><span data-stu-id="26da9-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="26da9-402">Não confie na entrada em chamadas interop JS em qualquer direção entre os métodos JavaScript e .NET.</span><span class="sxs-lookup"><span data-stu-id="26da9-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="26da9-403">O app é responsável por validar que o conteúdo dos argumentos e resultados são válidos, mesmo que os argumentos ou resultados sejam desserializados corretamente.</span><span class="sxs-lookup"><span data-stu-id="26da9-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="26da9-404">Para que exista uma vulnerabilidade XSS, o aplicativo deve incorporar a entrada do usuário na página renderizada.</span><span class="sxs-lookup"><span data-stu-id="26da9-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="26da9-405">Os componentes do servidor executam uma etapa de tempo de compilação onde a marcação em um arquivo *.razor* é transformada em lógica C# processual.</span><span class="sxs-lookup"><span data-stu-id="26da9-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="26da9-406">Em tempo de execução, a lógica C# constrói uma árvore de *renderização* descrevendo os elementos, texto e componentes da criança.</span><span class="sxs-lookup"><span data-stu-id="26da9-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="26da9-407">Isso é aplicado ao DOM do navegador através de uma seqüência de instruções JavaScript (ou é serializado em HTML no caso de pré-renderização):</span><span class="sxs-lookup"><span data-stu-id="26da9-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="26da9-408">A entrada do usuário renderizada via sintaxe de navalha normal (por exemplo, `@someStringValue`) não expõe uma vulnerabilidade XSS porque a sintaxe Razor é adicionada ao DOM através de comandos que só podem escrever texto.</span><span class="sxs-lookup"><span data-stu-id="26da9-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="26da9-409">Mesmo que o valor inclua marcação HTML, o valor é exibido como texto estático.</span><span class="sxs-lookup"><span data-stu-id="26da9-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="26da9-410">Ao pré-renderizar, a saída é codificada por HTML, que também exibe o conteúdo como texto estático.</span><span class="sxs-lookup"><span data-stu-id="26da9-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="26da9-411">As tags de script não são permitidas e não devem ser incluídas na árvore de renderização de componentes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="26da9-412">Se uma tag de script estiver incluída na marcação de um componente, um erro de tempo de compilação será gerado.</span><span class="sxs-lookup"><span data-stu-id="26da9-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="26da9-413">Os autores dos componentes podem escrever componentes em C# sem usar razor.</span><span class="sxs-lookup"><span data-stu-id="26da9-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="26da9-414">O autor do componente é responsável por usar as APIs corretas ao emitir saída.</span><span class="sxs-lookup"><span data-stu-id="26da9-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="26da9-415">Por exemplo, `builder.AddContent(0, someUserSuppliedString)` use e *não,* `builder.AddMarkupContent(0, someUserSuppliedString)`pois este último poderia criar uma vulnerabilidade XSS.</span><span class="sxs-lookup"><span data-stu-id="26da9-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="26da9-416">Como parte da proteção contra ataques XSS, considere implementar mitigações XSS, como [a Política de Segurança de Conteúdo (CSP).](https://developer.mozilla.org/docs/Web/HTTP/CSP)</span><span class="sxs-lookup"><span data-stu-id="26da9-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="26da9-417">Para obter mais informações, consulte <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="26da9-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="26da9-418">Proteção de origem cruzada</span><span class="sxs-lookup"><span data-stu-id="26da9-418">Cross-origin protection</span></span>

<span data-ttu-id="26da9-419">Ataques de origem cruzada envolvem um cliente de origem diferente realizando uma ação contra o servidor.</span><span class="sxs-lookup"><span data-stu-id="26da9-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="26da9-420">A ação maliciosa é tipicamente uma solicitação GET ou um formulário POST (Cross-Site Request Forgery, CSRF), mas abrir um WebSocket malicioso também é possível.</span><span class="sxs-lookup"><span data-stu-id="26da9-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="26da9-421">Os aplicativos do servidor oferecem [as mesmas garantias que qualquer outro SignalR aplicativo que use o protocolo hub oferece:](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="26da9-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="26da9-422">Os aplicativos do servidor podem ser acessados de origem cruzada, a menos que medidas adicionais sejam tomadas para impedi-lo.</span><span class="sxs-lookup"><span data-stu-id="26da9-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="26da9-423">Para desativar o `DisableCorsAttribute` Blazor acesso de origem cruzada, desabilite o CORS no ponto final adicionando o middleware CORS ao pipeline e adicionando os metadados ao ponto final ou limite o conjunto de origens permitidas [configurando SignalR para compartilhamento de recursos de origem cruzada](xref:signalr/security#cross-origin-resource-sharing).</span><span class="sxs-lookup"><span data-stu-id="26da9-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="26da9-424">Se o CORS estiver ativado, podem ser necessárias etapas extras para proteger o aplicativo, dependendo da configuração do CORS.</span><span class="sxs-lookup"><span data-stu-id="26da9-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="26da9-425">Se o CORS estiver globalmente habilitado, Blazor o CORS `DisableCorsAttribute` poderá ser desativado para o `hub.MapBlazorHub()`hub do Servidor adicionando os metadados aos metadados do ponto final após a chamada .</span><span class="sxs-lookup"><span data-stu-id="26da9-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="26da9-426">Para obter mais informações, consulte <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="26da9-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="26da9-427">Click-jacking</span><span class="sxs-lookup"><span data-stu-id="26da9-427">Click-jacking</span></span>

<span data-ttu-id="26da9-428">O click-jacking envolve renderizar um site como um `<iframe>` site de origem diferente, a fim de enganar o usuário para realizar ações no site sob ataque.</span><span class="sxs-lookup"><span data-stu-id="26da9-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="26da9-429">Para proteger um aplicativo de `<iframe>`renderização dentro de um [CSP (Content Security Policy, política](https://developer.mozilla.org/docs/Web/HTTP/CSP) de segurança de conteúdo) e o `X-Frame-Options` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="26da9-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="26da9-430">Para obter mais informações, consulte [os docs da Web MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span><span class="sxs-lookup"><span data-stu-id="26da9-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="26da9-431">Redirecionamentos abertos</span><span class="sxs-lookup"><span data-stu-id="26da9-431">Open redirects</span></span>

<span data-ttu-id="26da9-432">Quando Blazor uma sessão de aplicativo do Servidor é iniciada, o servidor executa a validação básica das URLs enviadas como parte do início da sessão.</span><span class="sxs-lookup"><span data-stu-id="26da9-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="26da9-433">A estrutura verifica se a URL base é um pai da URL atual antes de estabelecer o circuito.</span><span class="sxs-lookup"><span data-stu-id="26da9-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="26da9-434">Não são realizadas verificações adicionais pelo quadro.</span><span class="sxs-lookup"><span data-stu-id="26da9-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="26da9-435">Quando um usuário seleciona um link no cliente, a URL do link é enviada para o servidor, que determina que ação tomar.</span><span class="sxs-lookup"><span data-stu-id="26da9-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="26da9-436">Por exemplo, o aplicativo pode realizar uma navegação do lado do cliente ou indicar ao navegador para ir ao novo local.</span><span class="sxs-lookup"><span data-stu-id="26da9-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="26da9-437">Os componentes também podem desencadear solicitações `NavigationManager`de navegação de forma programática através do uso de .</span><span class="sxs-lookup"><span data-stu-id="26da9-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="26da9-438">Em tais cenários, o aplicativo pode realizar uma navegação do lado do cliente ou indicar ao navegador para ir ao novo local.</span><span class="sxs-lookup"><span data-stu-id="26da9-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="26da9-439">Os componentes devem:</span><span class="sxs-lookup"><span data-stu-id="26da9-439">Components must:</span></span>

* <span data-ttu-id="26da9-440">Evite usar a entrada do usuário como parte dos argumentos de chamada de navegação.</span><span class="sxs-lookup"><span data-stu-id="26da9-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="26da9-441">Valide argumentos para garantir que o destino seja permitido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26da9-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="26da9-442">Caso contrário, um usuário mal-intencionado pode forçar o navegador a ir para um site controlado por invasores.</span><span class="sxs-lookup"><span data-stu-id="26da9-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="26da9-443">Neste cenário, o invasor engana o aplicativo para usar alguma `NavigationManager.Navigate` entrada do usuário como parte da invocação do método.</span><span class="sxs-lookup"><span data-stu-id="26da9-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="26da9-444">Este conselho também se aplica ao renderizar links como parte do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="26da9-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="26da9-445">Se possível, use links relativos.</span><span class="sxs-lookup"><span data-stu-id="26da9-445">If possible, use relative links.</span></span>
* <span data-ttu-id="26da9-446">Valide que destinos de link absolutos são válidos antes de incluí-los em uma página.</span><span class="sxs-lookup"><span data-stu-id="26da9-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="26da9-447">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="26da9-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="26da9-448">Autenticação e autorização</span><span class="sxs-lookup"><span data-stu-id="26da9-448">Authentication and authorization</span></span>

<span data-ttu-id="26da9-449">Para obter orientação sobre <xref:security/blazor/index>autenticação e autorização, consulte .</span><span class="sxs-lookup"><span data-stu-id="26da9-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="26da9-450">Lista de verificação de segurança</span><span class="sxs-lookup"><span data-stu-id="26da9-450">Security checklist</span></span>

<span data-ttu-id="26da9-451">A seguinte lista de considerações de segurança não é abrangente:</span><span class="sxs-lookup"><span data-stu-id="26da9-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="26da9-452">Validar argumentos de eventos.</span><span class="sxs-lookup"><span data-stu-id="26da9-452">Validate arguments from events.</span></span>
* <span data-ttu-id="26da9-453">Validar entradas e resultados de chamadas interop JS.</span><span class="sxs-lookup"><span data-stu-id="26da9-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="26da9-454">Evite usar (ou validar antecipadamente) a entrada do usuário para chamadas .NET para JS interop.</span><span class="sxs-lookup"><span data-stu-id="26da9-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="26da9-455">Evite que o cliente aloque uma quantidade de memória não vinculada.</span><span class="sxs-lookup"><span data-stu-id="26da9-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="26da9-456">Dados dentro do componente.</span><span class="sxs-lookup"><span data-stu-id="26da9-456">Data within the component.</span></span>
  * <span data-ttu-id="26da9-457">`DotNetObject`referências devolvidas ao cliente.</span><span class="sxs-lookup"><span data-stu-id="26da9-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="26da9-458">Proteja-se contra vários despachos.</span><span class="sxs-lookup"><span data-stu-id="26da9-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="26da9-459">Cancele as operações de longa duração quando o componente estiver descartado.</span><span class="sxs-lookup"><span data-stu-id="26da9-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="26da9-460">Evite eventos que produzam grandes quantidades de dados.</span><span class="sxs-lookup"><span data-stu-id="26da9-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="26da9-461">Evite usar a entrada do `NavigationManager.Navigate` usuário como parte das chamadas e valide a entrada do usuário para URLs contra um conjunto de origens permitidas primeiro, se inevitável.</span><span class="sxs-lookup"><span data-stu-id="26da9-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="26da9-462">Não tome decisões de autorização com base no estado da UI, mas apenas do estado componente.</span><span class="sxs-lookup"><span data-stu-id="26da9-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="26da9-463">Considere usar [a Política de Segurança de Conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para proteger contra ataques XSS.</span><span class="sxs-lookup"><span data-stu-id="26da9-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="26da9-464">Considere usar CSP e [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) para proteger contra o click-jacking.</span><span class="sxs-lookup"><span data-stu-id="26da9-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="26da9-465">Certifique-se de que as configurações do CORS são Blazor apropriadas ao ativar o CORS ou desativar explicitamente o CORS para aplicativos.</span><span class="sxs-lookup"><span data-stu-id="26da9-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="26da9-466">Teste para garantir que os limites Blazor do lado do servidor para o aplicativo forneçam uma experiência de usuário aceitável sem níveis inaceitáveis de risco.</span><span class="sxs-lookup"><span data-stu-id="26da9-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
