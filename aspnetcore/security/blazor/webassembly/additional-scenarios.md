---
<span data-ttu-id="294ab-101">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-103">'Blazor'</span></span>
- <span data-ttu-id="294ab-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-104">'Identity'</span></span>
- <span data-ttu-id="294ab-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-106">'Razor'</span></span>
- <span data-ttu-id="294ab-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="294ab-108">BlazorCenários de segurança adicionais do Webassembly ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="294ab-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="294ab-109">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="294ab-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="294ab-110">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="294ab-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="294ab-111">O `AuthorizationMessageHandler` serviço pode ser usado com `HttpClient` para anexar tokens de acesso a solicitações de saída.</span><span class="sxs-lookup"><span data-stu-id="294ab-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="294ab-112">Os tokens são adquiridos usando o `IAccessTokenProvider` serviço existente.</span><span class="sxs-lookup"><span data-stu-id="294ab-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="294ab-113">Se um token não puder ser adquirido, um `AccessTokenNotAvailableException` será gerado.</span><span class="sxs-lookup"><span data-stu-id="294ab-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="294ab-114">`AccessTokenNotAvailableException`tem um `Redirect` método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.</span><span class="sxs-lookup"><span data-stu-id="294ab-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="294ab-115">O `AuthorizationMessageHandler` pode ser configurado com URLs autorizadas, escopos e URL de retorno usando `ConfigureHandler` o método.</span><span class="sxs-lookup"><span data-stu-id="294ab-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="294ab-116">No exemplo a seguir, `AuthorizationMessageHandler` configura um `HttpClient` in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="294ab-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
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

<span data-ttu-id="294ab-117">Para sua conveniência, `BaseAddressAuthorizationMessageHandler` é incluído um que é pré-configurado com o endereço base do aplicativo como uma URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="294ab-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="294ab-118">Os Blazor modelos Webassembly habilitados para autenticação agora usam <xref:System.Net.Http.IHttpClientFactory> no projeto de API do servidor para configurar um <xref:System.Net.Http.HttpClient> com o `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="294ab-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="294ab-119">Em que o cliente é criado `CreateClient` no exemplo anterior, as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="294ab-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="294ab-120"><xref:System.Net.Http.HttpClient>Em seguida, o configurado é usado para fazer solicitações autorizadas usando um `try-catch` padrão simples.</span><span class="sxs-lookup"><span data-stu-id="294ab-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="294ab-121">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="294ab-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="294ab-122">HttpClient digitado</span><span class="sxs-lookup"><span data-stu-id="294ab-122">Typed HttpClient</span></span>

<span data-ttu-id="294ab-123">Um cliente tipado pode ser definido para lidar com todas as preocupações de aquisição de token e HTTP em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="294ab-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="294ab-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="294ab-124">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="294ab-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="294ab-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="294ab-126">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="294ab-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="294ab-127">Configurar o manipulador HttpClient</span><span class="sxs-lookup"><span data-stu-id="294ab-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="294ab-128">O manipulador pode ser configurado ainda mais com <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="294ab-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="294ab-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="294ab-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="294ab-130">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="294ab-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="294ab-131">Se o Blazor aplicativo Webassembly normalmente usa um padrão seguro <xref:System.Net.Http.HttpClient> , o aplicativo também pode fazer solicitações de API da Web não autenticadas ou não autorizadas Configurando um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="294ab-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="294ab-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="294ab-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="294ab-133">O registro anterior é além do registro padrão seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="294ab-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="294ab-134">Um componente cria o <xref:System.Net.Http.HttpClient> do <xref:System.Net.Http.IHttpClientFactory> para fazer solicitações não autenticadas ou não autorizadas:</span><span class="sxs-lookup"><span data-stu-id="294ab-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="294ab-135">O controlador na API do servidor, `WeatherForecastNoAuthenticationController` para o exemplo anterior, não está marcado com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="294ab-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="294ab-136">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="294ab-136">Request additional access tokens</span></span>

<span data-ttu-id="294ab-137">Os tokens de acesso podem ser obtidos manualmente chamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="294ab-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="294ab-138">No exemplo a seguir, Azure Active Directory adicionais (AAD) Microsoft Graph escopos de API são exigidos por um aplicativo para ler dados do usuário e enviar email.</span><span class="sxs-lookup"><span data-stu-id="294ab-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="294ab-139">Depois de adicionar as permissões de API do Microsoft Graph no portal do Azure AAD, os escopos adicionais são configurados no aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="294ab-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="294ab-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="294ab-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="294ab-141">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos.</span><span class="sxs-lookup"><span data-stu-id="294ab-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="294ab-142">Em um Razor componente:</span><span class="sxs-lookup"><span data-stu-id="294ab-142">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

<span data-ttu-id="294ab-143">`TryGetToken`apresenta</span><span class="sxs-lookup"><span data-stu-id="294ab-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="294ab-144">`true`com o `token` para uso.</span><span class="sxs-lookup"><span data-stu-id="294ab-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="294ab-145">`false`Se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="294ab-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="294ab-146">HttpClient e HttpRequestMessage com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="294ab-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="294ab-147">Ao executar em Webassembly em um Blazor aplicativo Webassembly, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> pode ser usado para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="294ab-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="294ab-148">Por exemplo, você pode especificar o método HTTP e os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="294ab-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="294ab-149">O componente a seguir faz uma `POST` solicitação para um ponto de extremidade de API de lista de tarefas no servidor e mostra o corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="294ab-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="294ab-150">A implementação do .NET Webassembly de `HttpClient` usa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="294ab-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="294ab-151">FETCH permite configurar várias [opções específicas de solicitação](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="294ab-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="294ab-152">As opções de solicitação de busca HTTP podem ser configuradas com `HttpRequestMessage` métodos de extensão mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="294ab-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="294ab-153">`HttpRequestMessage`método de extensão</span><span class="sxs-lookup"><span data-stu-id="294ab-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="294ab-154">Buscar propriedade de solicitação</span><span class="sxs-lookup"><span data-stu-id="294ab-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="294ab-155">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-156">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-157">'Blazor'</span></span>
- <span data-ttu-id="294ab-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-158">'Identity'</span></span>
- <span data-ttu-id="294ab-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-160">'Razor'</span></span>
- <span data-ttu-id="294ab-161">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-162">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-163">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-164">'Blazor'</span></span>
- <span data-ttu-id="294ab-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-165">'Identity'</span></span>
- <span data-ttu-id="294ab-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-167">'Razor'</span></span>
- <span data-ttu-id="294ab-168">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-169">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-170">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-171">'Blazor'</span></span>
- <span data-ttu-id="294ab-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-172">'Identity'</span></span>
- <span data-ttu-id="294ab-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-174">'Razor'</span></span>
- <span data-ttu-id="294ab-175">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-176">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-177">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-178">'Blazor'</span></span>
- <span data-ttu-id="294ab-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-179">'Identity'</span></span>
- <span data-ttu-id="294ab-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-181">'Razor'</span></span>
- <span data-ttu-id="294ab-182">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-183">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-184">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-185">'Blazor'</span></span>
- <span data-ttu-id="294ab-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-186">'Identity'</span></span>
- <span data-ttu-id="294ab-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-188">'Razor'</span></span>
- <span data-ttu-id="294ab-189">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-190">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-191">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-192">'Blazor'</span></span>
- <span data-ttu-id="294ab-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-193">'Identity'</span></span>
- <span data-ttu-id="294ab-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-195">'Razor'</span></span>
- <span data-ttu-id="294ab-196">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-197">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-198">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-199">'Blazor'</span></span>
- <span data-ttu-id="294ab-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-200">'Identity'</span></span>
- <span data-ttu-id="294ab-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-202">'Razor'</span></span>
- <span data-ttu-id="294ab-203">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-204">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-205">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-206">'Blazor'</span></span>
- <span data-ttu-id="294ab-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-207">'Identity'</span></span>
- <span data-ttu-id="294ab-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-209">'Razor'</span></span>
- <span data-ttu-id="294ab-210">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-211">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-212">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-213">'Blazor'</span></span>
- <span data-ttu-id="294ab-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-214">'Identity'</span></span>
- <span data-ttu-id="294ab-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-216">'Razor'</span></span>
- <span data-ttu-id="294ab-217">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-218">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-219">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-220">'Blazor'</span></span>
- <span data-ttu-id="294ab-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-221">'Identity'</span></span>
- <span data-ttu-id="294ab-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-223">'Razor'</span></span>
- <span data-ttu-id="294ab-224">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-225">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-226">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-227">'Blazor'</span></span>
- <span data-ttu-id="294ab-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-228">'Identity'</span></span>
- <span data-ttu-id="294ab-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-230">'Razor'</span></span>
- <span data-ttu-id="294ab-231">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-232">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-233">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-234">'Blazor'</span></span>
- <span data-ttu-id="294ab-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-235">'Identity'</span></span>
- <span data-ttu-id="294ab-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-237">'Razor'</span></span>
- <span data-ttu-id="294ab-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-239">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-240">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-241">'Blazor'</span></span>
- <span data-ttu-id="294ab-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-242">'Identity'</span></span>
- <span data-ttu-id="294ab-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-244">'Razor'</span></span>
- <span data-ttu-id="294ab-245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-246">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-247">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-248">'Blazor'</span></span>
- <span data-ttu-id="294ab-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-249">'Identity'</span></span>
- <span data-ttu-id="294ab-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-251">'Razor'</span></span>
- <span data-ttu-id="294ab-252">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-253">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-254">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-255">'Blazor'</span></span>
- <span data-ttu-id="294ab-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-256">'Identity'</span></span>
- <span data-ttu-id="294ab-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-258">'Razor'</span></span>
- <span data-ttu-id="294ab-259">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-260">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-261">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-262">'Blazor'</span></span>
- <span data-ttu-id="294ab-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-263">'Identity'</span></span>
- <span data-ttu-id="294ab-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-265">'Razor'</span></span>
- <span data-ttu-id="294ab-266">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-266">'SignalR' uid:</span></span> 

<span data-ttu-id="294ab-267">------------------- | ---Título: ' ASP.NET Core Blazor cenários de segurança adicionais do Webassembly ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-268">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-269">'Blazor'</span></span>
- <span data-ttu-id="294ab-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-270">'Identity'</span></span>
- <span data-ttu-id="294ab-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-272">'Razor'</span></span>
- <span data-ttu-id="294ab-273">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-274">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-275">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-276">'Blazor'</span></span>
- <span data-ttu-id="294ab-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-277">'Identity'</span></span>
- <span data-ttu-id="294ab-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-279">'Razor'</span></span>
- <span data-ttu-id="294ab-280">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-281">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-282">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-283">'Blazor'</span></span>
- <span data-ttu-id="294ab-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-284">'Identity'</span></span>
- <span data-ttu-id="294ab-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-286">'Razor'</span></span>
- <span data-ttu-id="294ab-287">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-288">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-289">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-290">'Blazor'</span></span>
- <span data-ttu-id="294ab-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-291">'Identity'</span></span>
- <span data-ttu-id="294ab-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-293">'Razor'</span></span>
- <span data-ttu-id="294ab-294">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-295">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-296">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-297">'Blazor'</span></span>
- <span data-ttu-id="294ab-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-298">'Identity'</span></span>
- <span data-ttu-id="294ab-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-300">'Razor'</span></span>
- <span data-ttu-id="294ab-301">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-302">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-303">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-304">'Blazor'</span></span>
- <span data-ttu-id="294ab-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-305">'Identity'</span></span>
- <span data-ttu-id="294ab-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-307">'Razor'</span></span>
- <span data-ttu-id="294ab-308">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-309">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-310">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-311">'Blazor'</span></span>
- <span data-ttu-id="294ab-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-312">'Identity'</span></span>
- <span data-ttu-id="294ab-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-314">'Razor'</span></span>
- <span data-ttu-id="294ab-315">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-316">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-317">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-318">'Blazor'</span></span>
- <span data-ttu-id="294ab-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-319">'Identity'</span></span>
- <span data-ttu-id="294ab-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-321">'Razor'</span></span>
- <span data-ttu-id="294ab-322">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-323">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-324">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-325">'Blazor'</span></span>
- <span data-ttu-id="294ab-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-326">'Identity'</span></span>
- <span data-ttu-id="294ab-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-328">'Razor'</span></span>
- <span data-ttu-id="294ab-329">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-329">'SignalR' uid:</span></span> 

<span data-ttu-id="294ab-330">----------- | | `SetBrowserRequestCredentials`         |  [credenciais](https://developer.mozilla.org/docs/Web/API/Request/credentials) | `SetBrowserRequestCache` |               |  [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [modo](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [integridade](https://developer.mozilla.org/docs/Web/API/Request/integrity) do |</span><span class="sxs-lookup"><span data-stu-id="294ab-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="294ab-331">Você pode definir opções adicionais usando o método de `SetBrowserRequestOption` extensão mais genérico.</span><span class="sxs-lookup"><span data-stu-id="294ab-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="294ab-332">A resposta HTTP normalmente é armazenada em buffer em um Blazor aplicativo Webassembly para habilitar o suporte para leituras de sincronização no conteúdo da resposta.</span><span class="sxs-lookup"><span data-stu-id="294ab-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="294ab-333">Para habilitar o suporte para streaming de resposta, use o `SetBrowserResponseStreamingEnabled` método de extensão na solicitação.</span><span class="sxs-lookup"><span data-stu-id="294ab-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="294ab-334">Para incluir credenciais em uma solicitação entre origens, use o `SetBrowserRequestCredentials` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="294ab-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="294ab-335">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="294ab-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="294ab-336">Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações CORS, o `Authorization` cabeçalho deve ser permitido pela política CORS.</span><span class="sxs-lookup"><span data-stu-id="294ab-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="294ab-337">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="294ab-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="294ab-338">Origens da solicitação ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="294ab-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="294ab-339">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="294ab-339">Any method (verb).</span></span>
* <span data-ttu-id="294ab-340">`Content-Type``Authorization`cabeçalhos e.</span><span class="sxs-lookup"><span data-stu-id="294ab-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="294ab-341">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header` ), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="294ab-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="294ab-342">Credenciais definidas pelo código JavaScript do lado do cliente ( `credentials` propriedade definida como `include` ).</span><span class="sxs-lookup"><span data-stu-id="294ab-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="294ab-343">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="294ab-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="294ab-344">Manipular erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="294ab-344">Handle token request errors</span></span>

<span data-ttu-id="294ab-345">Quando um aplicativo de página única (SPA) autentica um usuário usando o OIDC (Open ID Connect), o estado de autenticação é mantido localmente no SPA e no Identity provedor (IP) na forma de um cookie de sessão que é definido como resultado do usuário que fornece suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="294ab-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="294ab-346">Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="294ab-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="294ab-347">Caso contrário, o usuário será desconectado após os tokens concedidos expirarem.</span><span class="sxs-lookup"><span data-stu-id="294ab-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="294ab-348">Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="294ab-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="294ab-349">Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP.</span><span class="sxs-lookup"><span data-stu-id="294ab-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="294ab-350">Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="294ab-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="294ab-351">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.</span><span class="sxs-lookup"><span data-stu-id="294ab-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="294ab-352">Esses cenários não são específicos para a autenticação baseada em token.</span><span class="sxs-lookup"><span data-stu-id="294ab-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="294ab-353">Eles fazem parte da natureza do SPAs.</span><span class="sxs-lookup"><span data-stu-id="294ab-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="294ab-354">Um SPA que usa cookies também falha ao chamar uma API de servidor se o cookie de autenticação for removido.</span><span class="sxs-lookup"><span data-stu-id="294ab-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="294ab-355">Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="294ab-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="294ab-356">Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="294ab-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="294ab-357">Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="294ab-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="294ab-358">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="294ab-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="294ab-359">A solicitação é realizada com sucesso e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="294ab-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="294ab-360">A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="294ab-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="294ab-361">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="294ab-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="294ab-362">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="294ab-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="294ab-363">Armazene o estado da página atual no armazenamento de sessão.</span><span class="sxs-lookup"><span data-stu-id="294ab-363">Store the current page state in session storage.</span></span> <span data-ttu-id="294ab-364">Durante `OnInitializeAsync` , verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="294ab-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="294ab-365">Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.</span><span class="sxs-lookup"><span data-stu-id="294ab-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="294ab-366">Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="294ab-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="294ab-367">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="294ab-367">The following example shows how to:</span></span>

* <span data-ttu-id="294ab-368">Preserve o estado antes de redirecionar para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="294ab-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="294ab-369">Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="294ab-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="294ab-370">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="294ab-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="294ab-371">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="294ab-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="294ab-372">Esse pode ser o caso quando você estiver usando algo como um contêiner de estado e desejar restaurar o estado depois que a autenticação for realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="294ab-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="294ab-373">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="294ab-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="294ab-374">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="294ab-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="294ab-375">Personalizar rotas de aplicativo</span><span class="sxs-lookup"><span data-stu-id="294ab-375">Customize app routes</span></span>

<span data-ttu-id="294ab-376">Por padrão, a `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="294ab-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="294ab-377">Rota</span><span class="sxs-lookup"><span data-stu-id="294ab-377">Route</span></span>                            | <span data-ttu-id="294ab-378">Finalidade</span><span class="sxs-lookup"><span data-stu-id="294ab-378">Purpose</span></span> |
| ---
<span data-ttu-id="294ab-379">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-380">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-381">'Blazor'</span></span>
- <span data-ttu-id="294ab-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-382">'Identity'</span></span>
- <span data-ttu-id="294ab-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-384">'Razor'</span></span>
- <span data-ttu-id="294ab-385">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-386">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-387">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-388">'Blazor'</span></span>
- <span data-ttu-id="294ab-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-389">'Identity'</span></span>
- <span data-ttu-id="294ab-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-391">'Razor'</span></span>
- <span data-ttu-id="294ab-392">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-393">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-394">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-395">'Blazor'</span></span>
- <span data-ttu-id="294ab-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-396">'Identity'</span></span>
- <span data-ttu-id="294ab-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-398">'Razor'</span></span>
- <span data-ttu-id="294ab-399">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-400">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-401">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-402">'Blazor'</span></span>
- <span data-ttu-id="294ab-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-403">'Identity'</span></span>
- <span data-ttu-id="294ab-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-405">'Razor'</span></span>
- <span data-ttu-id="294ab-406">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-407">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-408">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-409">'Blazor'</span></span>
- <span data-ttu-id="294ab-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-410">'Identity'</span></span>
- <span data-ttu-id="294ab-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-412">'Razor'</span></span>
- <span data-ttu-id="294ab-413">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-414">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-415">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-416">'Blazor'</span></span>
- <span data-ttu-id="294ab-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-417">'Identity'</span></span>
- <span data-ttu-id="294ab-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-419">'Razor'</span></span>
- <span data-ttu-id="294ab-420">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-421">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-422">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-423">'Blazor'</span></span>
- <span data-ttu-id="294ab-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-424">'Identity'</span></span>
- <span data-ttu-id="294ab-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-426">'Razor'</span></span>
- <span data-ttu-id="294ab-427">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-428">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-429">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-430">'Blazor'</span></span>
- <span data-ttu-id="294ab-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-431">'Identity'</span></span>
- <span data-ttu-id="294ab-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-433">'Razor'</span></span>
- <span data-ttu-id="294ab-434">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-435">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-436">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-437">'Blazor'</span></span>
- <span data-ttu-id="294ab-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-438">'Identity'</span></span>
- <span data-ttu-id="294ab-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-440">'Razor'</span></span>
- <span data-ttu-id="294ab-441">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-442">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-443">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-444">'Blazor'</span></span>
- <span data-ttu-id="294ab-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-445">'Identity'</span></span>
- <span data-ttu-id="294ab-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-447">'Razor'</span></span>
- <span data-ttu-id="294ab-448">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-449">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-450">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-451">'Blazor'</span></span>
- <span data-ttu-id="294ab-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-452">'Identity'</span></span>
- <span data-ttu-id="294ab-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-454">'Razor'</span></span>
- <span data-ttu-id="294ab-455">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-456">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-457">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-458">'Blazor'</span></span>
- <span data-ttu-id="294ab-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-459">'Identity'</span></span>
- <span data-ttu-id="294ab-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-461">'Razor'</span></span>
- <span data-ttu-id="294ab-462">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-463">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-464">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-465">'Blazor'</span></span>
- <span data-ttu-id="294ab-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-466">'Identity'</span></span>
- <span data-ttu-id="294ab-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-468">'Razor'</span></span>
- <span data-ttu-id="294ab-469">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-470">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-471">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-472">'Blazor'</span></span>
- <span data-ttu-id="294ab-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-473">'Identity'</span></span>
- <span data-ttu-id="294ab-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-475">'Razor'</span></span>
- <span data-ttu-id="294ab-476">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-476">'SignalR' uid:</span></span> 

<span data-ttu-id="294ab-477">---------------- | ---Título: ' ASP.NET Core Blazor cenários de segurança adicionais do Webassembly ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-478">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-479">'Blazor'</span></span>
- <span data-ttu-id="294ab-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-480">'Identity'</span></span>
- <span data-ttu-id="294ab-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-482">'Razor'</span></span>
- <span data-ttu-id="294ab-483">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-483">'SignalR' uid:</span></span> 

<span data-ttu-id="294ab-484">---- | | `authentication/login`           | Dispara uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="294ab-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="294ab-485">| | `authentication/login-callback`  | Manipula o resultado de qualquer operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="294ab-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="294ab-486">| | `authentication/login-failed`    | Exibe mensagens de erro quando a operação de entrada falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="294ab-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="294ab-487">| | `authentication/logout`          | Dispara uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="294ab-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="294ab-488">| | `authentication/logout-callback` | Lida com o resultado de uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="294ab-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="294ab-489">| | `authentication/logout-failed`   | Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="294ab-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="294ab-490">| | `authentication/logged-out`      | Indica que o usuário fez logoff com êxito.</span><span class="sxs-lookup"><span data-stu-id="294ab-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="294ab-491">| | `authentication/profile`         | Dispara uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="294ab-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="294ab-492">| | `authentication/register`        | Dispara uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="294ab-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="294ab-493">As rotas mostradas na tabela anterior são configuráveis via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="294ab-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="294ab-494">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.</span><span class="sxs-lookup"><span data-stu-id="294ab-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="294ab-495">No exemplo a seguir, todos os caminhos são prefixados com `/security` .</span><span class="sxs-lookup"><span data-stu-id="294ab-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="294ab-496">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="294ab-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="294ab-497">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="294ab-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="294ab-498">Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o `RemoteAuthenticatorView` com um parâmetro de ação explícita:</span><span class="sxs-lookup"><span data-stu-id="294ab-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="294ab-499">Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.</span><span class="sxs-lookup"><span data-stu-id="294ab-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="294ab-500">Personalizar a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="294ab-500">Customize the authentication user interface</span></span>

<span data-ttu-id="294ab-501">`RemoteAuthenticatorView`inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="294ab-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="294ab-502">Cada Estado pode ser personalizado passando um personalizado `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="294ab-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="294ab-503">Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o da `RemoteAuthenticatorView` seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="294ab-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="294ab-504">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="294ab-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="294ab-505">O `RemoteAuthenticatorView` tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="294ab-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="294ab-506">Rota</span><span class="sxs-lookup"><span data-stu-id="294ab-506">Route</span></span>                            | <span data-ttu-id="294ab-507">Fragmento</span><span class="sxs-lookup"><span data-stu-id="294ab-507">Fragment</span></span>                |
| ---
<span data-ttu-id="294ab-508">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-509">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-510">'Blazor'</span></span>
- <span data-ttu-id="294ab-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-511">'Identity'</span></span>
- <span data-ttu-id="294ab-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-513">'Razor'</span></span>
- <span data-ttu-id="294ab-514">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-515">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-516">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-517">'Blazor'</span></span>
- <span data-ttu-id="294ab-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-518">'Identity'</span></span>
- <span data-ttu-id="294ab-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-520">'Razor'</span></span>
- <span data-ttu-id="294ab-521">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-522">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-523">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-524">'Blazor'</span></span>
- <span data-ttu-id="294ab-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-525">'Identity'</span></span>
- <span data-ttu-id="294ab-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-527">'Razor'</span></span>
- <span data-ttu-id="294ab-528">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-529">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-530">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-531">'Blazor'</span></span>
- <span data-ttu-id="294ab-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-532">'Identity'</span></span>
- <span data-ttu-id="294ab-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-534">'Razor'</span></span>
- <span data-ttu-id="294ab-535">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-536">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-537">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-538">'Blazor'</span></span>
- <span data-ttu-id="294ab-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-539">'Identity'</span></span>
- <span data-ttu-id="294ab-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-541">'Razor'</span></span>
- <span data-ttu-id="294ab-542">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-543">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-544">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-545">'Blazor'</span></span>
- <span data-ttu-id="294ab-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-546">'Identity'</span></span>
- <span data-ttu-id="294ab-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-548">'Razor'</span></span>
- <span data-ttu-id="294ab-549">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-550">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-551">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-552">'Blazor'</span></span>
- <span data-ttu-id="294ab-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-553">'Identity'</span></span>
- <span data-ttu-id="294ab-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-555">'Razor'</span></span>
- <span data-ttu-id="294ab-556">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-557">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-558">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-559">'Blazor'</span></span>
- <span data-ttu-id="294ab-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-560">'Identity'</span></span>
- <span data-ttu-id="294ab-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-562">'Razor'</span></span>
- <span data-ttu-id="294ab-563">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-564">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-565">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-566">'Blazor'</span></span>
- <span data-ttu-id="294ab-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-567">'Identity'</span></span>
- <span data-ttu-id="294ab-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-569">'Razor'</span></span>
- <span data-ttu-id="294ab-570">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-571">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-572">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-573">'Blazor'</span></span>
- <span data-ttu-id="294ab-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-574">'Identity'</span></span>
- <span data-ttu-id="294ab-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-576">'Razor'</span></span>
- <span data-ttu-id="294ab-577">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-578">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-579">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-580">'Blazor'</span></span>
- <span data-ttu-id="294ab-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-581">'Identity'</span></span>
- <span data-ttu-id="294ab-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-583">'Razor'</span></span>
- <span data-ttu-id="294ab-584">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-585">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-586">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-587">'Blazor'</span></span>
- <span data-ttu-id="294ab-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-588">'Identity'</span></span>
- <span data-ttu-id="294ab-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-590">'Razor'</span></span>
- <span data-ttu-id="294ab-591">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-592">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-593">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-594">'Blazor'</span></span>
- <span data-ttu-id="294ab-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-595">'Identity'</span></span>
- <span data-ttu-id="294ab-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-597">'Razor'</span></span>
- <span data-ttu-id="294ab-598">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-599">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-600">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-601">'Blazor'</span></span>
- <span data-ttu-id="294ab-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-602">'Identity'</span></span>
- <span data-ttu-id="294ab-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-604">'Razor'</span></span>
- <span data-ttu-id="294ab-605">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-605">'SignalR' uid:</span></span> 

<span data-ttu-id="294ab-606">---------------- | ---Título: ' ASP.NET Core Blazor cenários de segurança adicionais do Webassembly ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-607">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-608">'Blazor'</span></span>
- <span data-ttu-id="294ab-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-609">'Identity'</span></span>
- <span data-ttu-id="294ab-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-611">'Razor'</span></span>
- <span data-ttu-id="294ab-612">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-613">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-614">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-615">'Blazor'</span></span>
- <span data-ttu-id="294ab-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-616">'Identity'</span></span>
- <span data-ttu-id="294ab-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-618">'Razor'</span></span>
- <span data-ttu-id="294ab-619">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-620">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-621">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-622">'Blazor'</span></span>
- <span data-ttu-id="294ab-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-623">'Identity'</span></span>
- <span data-ttu-id="294ab-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-625">'Razor'</span></span>
- <span data-ttu-id="294ab-626">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-627">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-628">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-629">'Blazor'</span></span>
- <span data-ttu-id="294ab-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-630">'Identity'</span></span>
- <span data-ttu-id="294ab-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-632">'Razor'</span></span>
- <span data-ttu-id="294ab-633">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-634">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-635">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-636">'Blazor'</span></span>
- <span data-ttu-id="294ab-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-637">'Identity'</span></span>
- <span data-ttu-id="294ab-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-639">'Razor'</span></span>
- <span data-ttu-id="294ab-640">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-641">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-642">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-643">'Blazor'</span></span>
- <span data-ttu-id="294ab-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-644">'Identity'</span></span>
- <span data-ttu-id="294ab-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-646">'Razor'</span></span>
- <span data-ttu-id="294ab-647">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-648">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-649">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-650">'Blazor'</span></span>
- <span data-ttu-id="294ab-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-651">'Identity'</span></span>
- <span data-ttu-id="294ab-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-653">'Razor'</span></span>
- <span data-ttu-id="294ab-654">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-655">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-656">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-657">'Blazor'</span></span>
- <span data-ttu-id="294ab-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-658">'Identity'</span></span>
- <span data-ttu-id="294ab-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-660">'Razor'</span></span>
- <span data-ttu-id="294ab-661">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="294ab-662">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="294ab-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="294ab-663">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="294ab-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="294ab-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="294ab-664">'Blazor'</span></span>
- <span data-ttu-id="294ab-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="294ab-665">'Identity'</span></span>
- <span data-ttu-id="294ab-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="294ab-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="294ab-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="294ab-667">'Razor'</span></span>
- <span data-ttu-id="294ab-668">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="294ab-668">'SignalR' uid:</span></span> 

<span data-ttu-id="294ab-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="294ab-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="294ab-670">Personalizar o usuário</span><span class="sxs-lookup"><span data-stu-id="294ab-670">Customize the user</span></span>

<span data-ttu-id="294ab-671">Os usuários associados ao aplicativo podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="294ab-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="294ab-672">No exemplo a seguir, todos os usuários autenticados recebem uma `amr` declaração para cada um dos métodos de autenticação do usuário.</span><span class="sxs-lookup"><span data-stu-id="294ab-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="294ab-673">Crie uma classe que estenda a `RemoteUserAccount` classe:</span><span class="sxs-lookup"><span data-stu-id="294ab-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="294ab-674">Crie uma fábrica que estenda `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="294ab-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
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

<span data-ttu-id="294ab-675">Registre o `CustomAccountFactory` para o provedor de autenticação em uso.</span><span class="sxs-lookup"><span data-stu-id="294ab-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="294ab-676">Qualquer um dos seguintes registros é válido:</span><span class="sxs-lookup"><span data-stu-id="294ab-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="294ab-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="294ab-677">`AddOidcAuthentication`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="294ab-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="294ab-678">`AddMsalAuthentication`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="294ab-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="294ab-679">`AddApiAuthorization`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="294ab-680">Suporte ao pré-processamento com autenticação</span><span class="sxs-lookup"><span data-stu-id="294ab-680">Support prerendering with authentication</span></span>

<span data-ttu-id="294ab-681">Depois de seguir as diretrizes em um dos Blazor Tópicos do aplicativo Webassembly hospedado, use as instruções a seguir para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="294ab-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="294ab-682">Processa caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="294ab-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="294ab-683">Não PreRender caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="294ab-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="294ab-684">Na classe do aplicativo cliente `Program` (*Program.cs*), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="294ab-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

<span data-ttu-id="294ab-685">No aplicativo do servidor `Startup.ConfigureServices` , registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="294ab-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="294ab-686">No método do aplicativo do servidor `Startup.Configure` , substitua `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="294ab-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="294ab-687">No aplicativo de servidor, crie uma pasta de *páginas* se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="294ab-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="294ab-688">Crie uma página *_Host. cshtml* dentro da pasta *Páginas* do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="294ab-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="294ab-689">Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo cliente no arquivo *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="294ab-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="294ab-690">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="294ab-690">Update the file's contents:</span></span>

* <span data-ttu-id="294ab-691">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="294ab-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="294ab-692">Substitua a `<app>Loading...</app>` marca pela seguinte:</span><span class="sxs-lookup"><span data-stu-id="294ab-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="294ab-693">Opções para aplicativos hospedados e provedores de logon de terceiros</span><span class="sxs-lookup"><span data-stu-id="294ab-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="294ab-694">Ao autenticar e autorizar um Blazor aplicativo Webassembly hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="294ab-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="294ab-695">O que você escolher dependerá de seu cenário.</span><span class="sxs-lookup"><span data-stu-id="294ab-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="294ab-696">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="294ab-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="294ab-697">Autenticar usuários para chamar somente APIs de terceiros protegidas</span><span class="sxs-lookup"><span data-stu-id="294ab-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="294ab-698">Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="294ab-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="294ab-699">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="294ab-699">In this scenario:</span></span>

* <span data-ttu-id="294ab-700">O servidor que hospeda o aplicativo não desempenha uma função.</span><span class="sxs-lookup"><span data-stu-id="294ab-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="294ab-701">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="294ab-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="294ab-702">O aplicativo só pode chamar APIs de terceiros protegidas.</span><span class="sxs-lookup"><span data-stu-id="294ab-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="294ab-703">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros</span><span class="sxs-lookup"><span data-stu-id="294ab-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="294ab-704">Configure Identity com um provedor de logon de terceiros.</span><span class="sxs-lookup"><span data-stu-id="294ab-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="294ab-705">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="294ab-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="294ab-706">Quando um usuário faz logon, o Identity coleta tokens de acesso e de atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="294ab-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="294ab-707">Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="294ab-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="294ab-708">Usar um token de acesso do servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="294ab-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="294ab-709">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="294ab-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="294ab-710">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente do Identity no cliente.</span><span class="sxs-lookup"><span data-stu-id="294ab-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="294ab-711">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="294ab-711">We don't recommend this approach.</span></span> <span data-ttu-id="294ab-712">Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="294ab-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="294ab-713">Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="294ab-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="294ab-714">Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="294ab-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="294ab-715">O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="294ab-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="294ab-716">Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.</span><span class="sxs-lookup"><span data-stu-id="294ab-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="294ab-717">Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="294ab-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="294ab-718">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="294ab-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="294ab-719">No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="294ab-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="294ab-720">Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="294ab-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="294ab-721">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="294ab-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="294ab-722">O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="294ab-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="294ab-723">Usar pontos de extremidade do OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="294ab-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="294ab-724">A biblioteca de autenticação e os Blazor modelos usam pontos de extremidade do OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="294ab-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="294ab-725">Para usar um ponto de extremidade v 2.0, configure a opção de portador JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="294ab-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="294ab-726">No exemplo a seguir, o AAD está configurado para v 2.0 acrescentando um `v2.0` segmento à `Authority` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="294ab-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="294ab-727">Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="294ab-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="294ab-728">Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a `Authority` propriedade diretamente.</span><span class="sxs-lookup"><span data-stu-id="294ab-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="294ab-729">Defina a propriedade no `JwtBearerOptions` ou no arquivo de configurações do aplicativo com a `Authority` chave.</span><span class="sxs-lookup"><span data-stu-id="294ab-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="294ab-730">A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0.</span><span class="sxs-lookup"><span data-stu-id="294ab-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="294ab-731">Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="294ab-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
