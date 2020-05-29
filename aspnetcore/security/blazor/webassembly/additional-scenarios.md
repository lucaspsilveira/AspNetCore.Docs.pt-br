---
<span data-ttu-id="40cf5-101">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-103">'Blazor'</span></span>
- <span data-ttu-id="40cf5-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-104">'Identity'</span></span>
- <span data-ttu-id="40cf5-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-106">'Razor'</span></span>
- <span data-ttu-id="40cf5-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="40cf5-108">BlazorCenários de segurança adicionais do Webassembly ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40cf5-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="40cf5-109">Por [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="40cf5-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="40cf5-110">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="40cf5-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="40cf5-111">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> serviço pode ser usado com <xref:System.Net.Http.HttpClient> para anexar tokens de acesso a solicitações de saída.</span><span class="sxs-lookup"><span data-stu-id="40cf5-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="40cf5-112">Os tokens são adquiridos usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> serviço existente.</span><span class="sxs-lookup"><span data-stu-id="40cf5-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="40cf5-113">Se um token não puder ser adquirido, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> será gerado.</span><span class="sxs-lookup"><span data-stu-id="40cf5-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="40cf5-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>tem um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.</span><span class="sxs-lookup"><span data-stu-id="40cf5-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="40cf5-115">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pode ser configurado com URLs autorizadas, escopos e URL de retorno usando <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> o método.</span><span class="sxs-lookup"><span data-stu-id="40cf5-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="40cf5-116">No exemplo a seguir, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura um <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="40cf5-117">Para sua conveniência, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> é incluído um que é pré-configurado com o endereço base do aplicativo como uma URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="40cf5-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="40cf5-118">Os Blazor modelos Webassembly habilitados para autenticação agora usam <xref:System.Net.Http.IHttpClientFactory> no projeto de API do servidor para configurar um <xref:System.Net.Http.HttpClient> com o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="40cf5-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="40cf5-119">Em que o cliente é criado <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> no exemplo anterior, as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="40cf5-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="40cf5-120"><xref:System.Net.Http.HttpClient>Em seguida, o configurado é usado para fazer solicitações autorizadas usando um padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) simples.</span><span class="sxs-lookup"><span data-stu-id="40cf5-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="40cf5-121">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="40cf5-122">HttpClient digitado</span><span class="sxs-lookup"><span data-stu-id="40cf5-122">Typed HttpClient</span></span>

<span data-ttu-id="40cf5-123">Um cliente tipado pode ser definido para lidar com todas as preocupações de aquisição de token e HTTP em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="40cf5-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="40cf5-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="40cf5-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="40cf5-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="40cf5-126">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="40cf5-127">Configurar o manipulador HttpClient</span><span class="sxs-lookup"><span data-stu-id="40cf5-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="40cf5-128">O manipulador pode ser configurado ainda mais com <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="40cf5-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="40cf5-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="40cf5-130">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="40cf5-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="40cf5-131">Se o Blazor aplicativo Webassembly normalmente usa um padrão seguro <xref:System.Net.Http.HttpClient> , o aplicativo também pode fazer solicitações de API da Web não autenticadas ou não autorizadas Configurando um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="40cf5-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="40cf5-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="40cf5-133">O registro anterior é além do registro padrão seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="40cf5-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="40cf5-134">Um componente cria o <xref:System.Net.Http.HttpClient> do <xref:System.Net.Http.IHttpClientFactory> para fazer solicitações não autenticadas ou não autorizadas:</span><span class="sxs-lookup"><span data-stu-id="40cf5-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="40cf5-135">O controlador na API do servidor, `WeatherForecastNoAuthenticationController` para o exemplo anterior, não está marcado com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="40cf5-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="40cf5-136">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="40cf5-136">Request additional access tokens</span></span>

<span data-ttu-id="40cf5-137">Os tokens de acesso podem ser obtidos manualmente chamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="40cf5-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="40cf5-138">No exemplo a seguir, Azure Active Directory adicionais (AAD) Microsoft Graph escopos de API são exigidos por um aplicativo para ler dados do usuário e enviar email.</span><span class="sxs-lookup"><span data-stu-id="40cf5-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="40cf5-139">Depois de adicionar as permissões de API do Microsoft Graph no portal do Azure AAD, os escopos adicionais são configurados no aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="40cf5-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="40cf5-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="40cf5-141">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos.</span><span class="sxs-lookup"><span data-stu-id="40cf5-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="40cf5-142">Em um Razor componente:</span><span class="sxs-lookup"><span data-stu-id="40cf5-142">In a Razor component:</span></span>

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

<span data-ttu-id="40cf5-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>apresenta</span><span class="sxs-lookup"><span data-stu-id="40cf5-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="40cf5-144">`true`com o `token` para uso.</span><span class="sxs-lookup"><span data-stu-id="40cf5-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="40cf5-145">`false`Se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="40cf5-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="40cf5-146">HttpClient e HttpRequestMessage com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="40cf5-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="40cf5-147">Ao executar em Webassembly em um Blazor aplicativo Webassembly, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> pode ser usado para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="40cf5-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="40cf5-148">Por exemplo, você pode especificar o método HTTP e os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="40cf5-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="40cf5-149">O componente a seguir faz uma `POST` solicitação para um ponto de extremidade de API de lista de tarefas no servidor e mostra o corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="40cf5-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="40cf5-150">A implementação do .NET Webassembly de <xref:System.Net.Http.HttpClient> usa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="40cf5-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="40cf5-151">FETCH permite configurar várias [opções específicas de solicitação](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="40cf5-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="40cf5-152">As opções de solicitação de busca HTTP podem ser configuradas com <xref:System.Net.Http.HttpRequestMessage> métodos de extensão mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="40cf5-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="40cf5-153">Método de extensão</span><span class="sxs-lookup"><span data-stu-id="40cf5-153">Extension method</span></span> | <span data-ttu-id="40cf5-154">Buscar propriedade de solicitação</span><span class="sxs-lookup"><span data-stu-id="40cf5-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="40cf5-155">fornecidas</span><span class="sxs-lookup"><span data-stu-id="40cf5-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="40cf5-156">armazenar</span><span class="sxs-lookup"><span data-stu-id="40cf5-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="40cf5-157">mode</span><span class="sxs-lookup"><span data-stu-id="40cf5-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="40cf5-158">verifica</span><span class="sxs-lookup"><span data-stu-id="40cf5-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="40cf5-159">Você pode definir opções adicionais usando o método de <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extensão mais genérico.</span><span class="sxs-lookup"><span data-stu-id="40cf5-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="40cf5-160">A resposta HTTP normalmente é armazenada em buffer em um Blazor aplicativo Webassembly para habilitar o suporte para leituras de sincronização no conteúdo da resposta.</span><span class="sxs-lookup"><span data-stu-id="40cf5-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="40cf5-161">Para habilitar o suporte para streaming de resposta, use o <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> método de extensão na solicitação.</span><span class="sxs-lookup"><span data-stu-id="40cf5-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="40cf5-162">Para incluir credenciais em uma solicitação entre origens, use o <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="40cf5-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="40cf5-163">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="40cf5-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="40cf5-164">Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações CORS, o `Authorization` cabeçalho deve ser permitido pela política CORS.</span><span class="sxs-lookup"><span data-stu-id="40cf5-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="40cf5-165">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="40cf5-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="40cf5-166">Origens da solicitação ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="40cf5-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="40cf5-167">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="40cf5-167">Any method (verb).</span></span>
* <span data-ttu-id="40cf5-168">`Content-Type``Authorization`cabeçalhos e.</span><span class="sxs-lookup"><span data-stu-id="40cf5-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="40cf5-169">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header` ), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="40cf5-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="40cf5-170">Credenciais definidas pelo código JavaScript do lado do cliente ( `credentials` propriedade definida como `include` ).</span><span class="sxs-lookup"><span data-stu-id="40cf5-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="40cf5-171">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="40cf5-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="40cf5-172">Manipular erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="40cf5-172">Handle token request errors</span></span>

<span data-ttu-id="40cf5-173">Quando um aplicativo de página única (SPA) autentica um usuário usando o OIDC (Open ID Connect), o estado de autenticação é mantido localmente no SPA e no Identity provedor (IP) na forma de um cookie de sessão que é definido como resultado do usuário que fornece suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="40cf5-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="40cf5-174">Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="40cf5-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="40cf5-175">Caso contrário, o usuário será desconectado após os tokens concedidos expirarem.</span><span class="sxs-lookup"><span data-stu-id="40cf5-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="40cf5-176">Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="40cf5-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="40cf5-177">Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP.</span><span class="sxs-lookup"><span data-stu-id="40cf5-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="40cf5-178">Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="40cf5-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="40cf5-179">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.</span><span class="sxs-lookup"><span data-stu-id="40cf5-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="40cf5-180">Esses cenários não são específicos para a autenticação baseada em token.</span><span class="sxs-lookup"><span data-stu-id="40cf5-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="40cf5-181">Eles fazem parte da natureza do SPAs.</span><span class="sxs-lookup"><span data-stu-id="40cf5-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="40cf5-182">Um SPA que usa cookies também falha ao chamar uma API de servidor se o cookie de autenticação for removido.</span><span class="sxs-lookup"><span data-stu-id="40cf5-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="40cf5-183">Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="40cf5-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="40cf5-184">Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="40cf5-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="40cf5-185">Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="40cf5-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="40cf5-186">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="40cf5-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="40cf5-187">A solicitação é realizada com sucesso e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="40cf5-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="40cf5-188">A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="40cf5-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="40cf5-189">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="40cf5-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="40cf5-190">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="40cf5-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="40cf5-191">Armazene o estado da página atual no armazenamento de sessão.</span><span class="sxs-lookup"><span data-stu-id="40cf5-191">Store the current page state in session storage.</span></span> <span data-ttu-id="40cf5-192">Durante o [evento de ciclo de vida OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="40cf5-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="40cf5-193">Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.</span><span class="sxs-lookup"><span data-stu-id="40cf5-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="40cf5-194">Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="40cf5-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="40cf5-195">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="40cf5-195">The following example shows how to:</span></span>

* <span data-ttu-id="40cf5-196">Preserve o estado antes de redirecionar para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="40cf5-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="40cf5-197">Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="40cf5-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="40cf5-198">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="40cf5-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="40cf5-199">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="40cf5-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="40cf5-200">Esse pode ser o caso quando você estiver usando algo como um contêiner de estado e desejar restaurar o estado depois que a autenticação for realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="40cf5-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="40cf5-201">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="40cf5-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="40cf5-202">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="40cf5-203">Personalizar rotas de aplicativo</span><span class="sxs-lookup"><span data-stu-id="40cf5-203">Customize app routes</span></span>

<span data-ttu-id="40cf5-204">Por padrão, a biblioteca [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="40cf5-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="40cf5-205">Rota</span><span class="sxs-lookup"><span data-stu-id="40cf5-205">Route</span></span>                            | <span data-ttu-id="40cf5-206">Finalidade</span><span class="sxs-lookup"><span data-stu-id="40cf5-206">Purpose</span></span> |
| ---
<span data-ttu-id="40cf5-207">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-208">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-209">'Blazor'</span></span>
- <span data-ttu-id="40cf5-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-210">'Identity'</span></span>
- <span data-ttu-id="40cf5-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-212">'Razor'</span></span>
- <span data-ttu-id="40cf5-213">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-214">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-215">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-216">'Blazor'</span></span>
- <span data-ttu-id="40cf5-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-217">'Identity'</span></span>
- <span data-ttu-id="40cf5-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-219">'Razor'</span></span>
- <span data-ttu-id="40cf5-220">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-221">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-222">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-223">'Blazor'</span></span>
- <span data-ttu-id="40cf5-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-224">'Identity'</span></span>
- <span data-ttu-id="40cf5-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-226">'Razor'</span></span>
- <span data-ttu-id="40cf5-227">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-228">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-229">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-230">'Blazor'</span></span>
- <span data-ttu-id="40cf5-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-231">'Identity'</span></span>
- <span data-ttu-id="40cf5-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-233">'Razor'</span></span>
- <span data-ttu-id="40cf5-234">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-235">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-236">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-237">'Blazor'</span></span>
- <span data-ttu-id="40cf5-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-238">'Identity'</span></span>
- <span data-ttu-id="40cf5-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-240">'Razor'</span></span>
- <span data-ttu-id="40cf5-241">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-242">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-243">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-244">'Blazor'</span></span>
- <span data-ttu-id="40cf5-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-245">'Identity'</span></span>
- <span data-ttu-id="40cf5-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-247">'Razor'</span></span>
- <span data-ttu-id="40cf5-248">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-249">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-250">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-251">'Blazor'</span></span>
- <span data-ttu-id="40cf5-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-252">'Identity'</span></span>
- <span data-ttu-id="40cf5-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-254">'Razor'</span></span>
- <span data-ttu-id="40cf5-255">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-256">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-257">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-258">'Blazor'</span></span>
- <span data-ttu-id="40cf5-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-259">'Identity'</span></span>
- <span data-ttu-id="40cf5-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-261">'Razor'</span></span>
- <span data-ttu-id="40cf5-262">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-263">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-264">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-265">'Blazor'</span></span>
- <span data-ttu-id="40cf5-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-266">'Identity'</span></span>
- <span data-ttu-id="40cf5-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-268">'Razor'</span></span>
- <span data-ttu-id="40cf5-269">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-270">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-271">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-272">'Blazor'</span></span>
- <span data-ttu-id="40cf5-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-273">'Identity'</span></span>
- <span data-ttu-id="40cf5-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-275">'Razor'</span></span>
- <span data-ttu-id="40cf5-276">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-277">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-278">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-279">'Blazor'</span></span>
- <span data-ttu-id="40cf5-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-280">'Identity'</span></span>
- <span data-ttu-id="40cf5-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-282">'Razor'</span></span>
- <span data-ttu-id="40cf5-283">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-284">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-285">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-286">'Blazor'</span></span>
- <span data-ttu-id="40cf5-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-287">'Identity'</span></span>
- <span data-ttu-id="40cf5-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-289">'Razor'</span></span>
- <span data-ttu-id="40cf5-290">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-291">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-292">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-293">'Blazor'</span></span>
- <span data-ttu-id="40cf5-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-294">'Identity'</span></span>
- <span data-ttu-id="40cf5-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-296">'Razor'</span></span>
- <span data-ttu-id="40cf5-297">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-298">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-299">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-300">'Blazor'</span></span>
- <span data-ttu-id="40cf5-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-301">'Identity'</span></span>
- <span data-ttu-id="40cf5-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-303">'Razor'</span></span>
- <span data-ttu-id="40cf5-304">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-304">'SignalR' uid:</span></span> 

<span data-ttu-id="40cf5-305">---------------- | ---Título: ' ASP.NET Core Blazor cenários de segurança adicionais do Webassembly ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-306">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-307">'Blazor'</span></span>
- <span data-ttu-id="40cf5-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-308">'Identity'</span></span>
- <span data-ttu-id="40cf5-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-310">'Razor'</span></span>
- <span data-ttu-id="40cf5-311">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-311">'SignalR' uid:</span></span> 

<span data-ttu-id="40cf5-312">---- | | `authentication/login`           | Dispara uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="40cf5-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="40cf5-313">| | `authentication/login-callback`  | Manipula o resultado de qualquer operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="40cf5-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="40cf5-314">| | `authentication/login-failed`    | Exibe mensagens de erro quando a operação de entrada falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="40cf5-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="40cf5-315">| | `authentication/logout`          | Dispara uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="40cf5-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="40cf5-316">| | `authentication/logout-callback` | Lida com o resultado de uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="40cf5-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="40cf5-317">| | `authentication/logout-failed`   | Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="40cf5-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="40cf5-318">| | `authentication/logged-out`      | Indica que o usuário fez logoff com êxito.</span><span class="sxs-lookup"><span data-stu-id="40cf5-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="40cf5-319">| | `authentication/profile`         | Dispara uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="40cf5-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="40cf5-320">| | `authentication/register`        | Dispara uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="40cf5-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="40cf5-321">As rotas mostradas na tabela anterior são configuráveis via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="40cf5-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="40cf5-322">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.</span><span class="sxs-lookup"><span data-stu-id="40cf5-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="40cf5-323">No exemplo a seguir, todos os caminhos são prefixados com `/security` .</span><span class="sxs-lookup"><span data-stu-id="40cf5-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="40cf5-324">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="40cf5-325">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="40cf5-326">Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> com um parâmetro de ação explícita:</span><span class="sxs-lookup"><span data-stu-id="40cf5-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="40cf5-327">Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.</span><span class="sxs-lookup"><span data-stu-id="40cf5-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="40cf5-328">Personalizar a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="40cf5-328">Customize the authentication user interface</span></span>

<span data-ttu-id="40cf5-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="40cf5-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="40cf5-330">Cada Estado pode ser personalizado passando um personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="40cf5-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="40cf5-331">Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o da <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="40cf5-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="40cf5-332">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="40cf5-333">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="40cf5-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="40cf5-334">Rota</span><span class="sxs-lookup"><span data-stu-id="40cf5-334">Route</span></span>                            | <span data-ttu-id="40cf5-335">Fragmento</span><span class="sxs-lookup"><span data-stu-id="40cf5-335">Fragment</span></span>                |
| ---
<span data-ttu-id="40cf5-336">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-337">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-338">'Blazor'</span></span>
- <span data-ttu-id="40cf5-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-339">'Identity'</span></span>
- <span data-ttu-id="40cf5-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-341">'Razor'</span></span>
- <span data-ttu-id="40cf5-342">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-343">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-344">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-345">'Blazor'</span></span>
- <span data-ttu-id="40cf5-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-346">'Identity'</span></span>
- <span data-ttu-id="40cf5-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-348">'Razor'</span></span>
- <span data-ttu-id="40cf5-349">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-350">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-351">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-352">'Blazor'</span></span>
- <span data-ttu-id="40cf5-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-353">'Identity'</span></span>
- <span data-ttu-id="40cf5-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-355">'Razor'</span></span>
- <span data-ttu-id="40cf5-356">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-357">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-358">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-359">'Blazor'</span></span>
- <span data-ttu-id="40cf5-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-360">'Identity'</span></span>
- <span data-ttu-id="40cf5-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-362">'Razor'</span></span>
- <span data-ttu-id="40cf5-363">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-364">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-365">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-366">'Blazor'</span></span>
- <span data-ttu-id="40cf5-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-367">'Identity'</span></span>
- <span data-ttu-id="40cf5-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-369">'Razor'</span></span>
- <span data-ttu-id="40cf5-370">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-371">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-372">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-373">'Blazor'</span></span>
- <span data-ttu-id="40cf5-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-374">'Identity'</span></span>
- <span data-ttu-id="40cf5-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-376">'Razor'</span></span>
- <span data-ttu-id="40cf5-377">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-378">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-379">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-380">'Blazor'</span></span>
- <span data-ttu-id="40cf5-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-381">'Identity'</span></span>
- <span data-ttu-id="40cf5-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-383">'Razor'</span></span>
- <span data-ttu-id="40cf5-384">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-385">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-386">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-387">'Blazor'</span></span>
- <span data-ttu-id="40cf5-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-388">'Identity'</span></span>
- <span data-ttu-id="40cf5-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-390">'Razor'</span></span>
- <span data-ttu-id="40cf5-391">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-392">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-393">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-394">'Blazor'</span></span>
- <span data-ttu-id="40cf5-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-395">'Identity'</span></span>
- <span data-ttu-id="40cf5-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-397">'Razor'</span></span>
- <span data-ttu-id="40cf5-398">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-399">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-400">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-401">'Blazor'</span></span>
- <span data-ttu-id="40cf5-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-402">'Identity'</span></span>
- <span data-ttu-id="40cf5-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-404">'Razor'</span></span>
- <span data-ttu-id="40cf5-405">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-406">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-407">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-408">'Blazor'</span></span>
- <span data-ttu-id="40cf5-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-409">'Identity'</span></span>
- <span data-ttu-id="40cf5-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-411">'Razor'</span></span>
- <span data-ttu-id="40cf5-412">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-413">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-414">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-415">'Blazor'</span></span>
- <span data-ttu-id="40cf5-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-416">'Identity'</span></span>
- <span data-ttu-id="40cf5-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-418">'Razor'</span></span>
- <span data-ttu-id="40cf5-419">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-420">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-421">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-422">'Blazor'</span></span>
- <span data-ttu-id="40cf5-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-423">'Identity'</span></span>
- <span data-ttu-id="40cf5-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-425">'Razor'</span></span>
- <span data-ttu-id="40cf5-426">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-427">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-428">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-429">'Blazor'</span></span>
- <span data-ttu-id="40cf5-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-430">'Identity'</span></span>
- <span data-ttu-id="40cf5-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-432">'Razor'</span></span>
- <span data-ttu-id="40cf5-433">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-433">'SignalR' uid:</span></span> 

<span data-ttu-id="40cf5-434">---------------- | ---Título: ' ASP.NET Core Blazor cenários de segurança adicionais do Webassembly ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-435">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-436">'Blazor'</span></span>
- <span data-ttu-id="40cf5-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-437">'Identity'</span></span>
- <span data-ttu-id="40cf5-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-439">'Razor'</span></span>
- <span data-ttu-id="40cf5-440">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-441">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-442">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-443">'Blazor'</span></span>
- <span data-ttu-id="40cf5-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-444">'Identity'</span></span>
- <span data-ttu-id="40cf5-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-446">'Razor'</span></span>
- <span data-ttu-id="40cf5-447">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-448">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-449">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-450">'Blazor'</span></span>
- <span data-ttu-id="40cf5-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-451">'Identity'</span></span>
- <span data-ttu-id="40cf5-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-453">'Razor'</span></span>
- <span data-ttu-id="40cf5-454">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-455">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-456">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-457">'Blazor'</span></span>
- <span data-ttu-id="40cf5-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-458">'Identity'</span></span>
- <span data-ttu-id="40cf5-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-460">'Razor'</span></span>
- <span data-ttu-id="40cf5-461">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-462">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-463">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-464">'Blazor'</span></span>
- <span data-ttu-id="40cf5-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-465">'Identity'</span></span>
- <span data-ttu-id="40cf5-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-467">'Razor'</span></span>
- <span data-ttu-id="40cf5-468">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-469">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-470">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-471">'Blazor'</span></span>
- <span data-ttu-id="40cf5-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-472">'Identity'</span></span>
- <span data-ttu-id="40cf5-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-474">'Razor'</span></span>
- <span data-ttu-id="40cf5-475">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-476">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-477">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-478">'Blazor'</span></span>
- <span data-ttu-id="40cf5-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-479">'Identity'</span></span>
- <span data-ttu-id="40cf5-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-481">'Razor'</span></span>
- <span data-ttu-id="40cf5-482">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-483">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-484">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-485">'Blazor'</span></span>
- <span data-ttu-id="40cf5-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-486">'Identity'</span></span>
- <span data-ttu-id="40cf5-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-488">'Razor'</span></span>
- <span data-ttu-id="40cf5-489">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40cf5-490">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="40cf5-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="40cf5-491">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40cf5-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40cf5-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-492">'Blazor'</span></span>
- <span data-ttu-id="40cf5-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40cf5-493">'Identity'</span></span>
- <span data-ttu-id="40cf5-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40cf5-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="40cf5-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40cf5-495">'Razor'</span></span>
- <span data-ttu-id="40cf5-496">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40cf5-496">'SignalR' uid:</span></span> 

<span data-ttu-id="40cf5-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="40cf5-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="40cf5-498">Personalizar o usuário</span><span class="sxs-lookup"><span data-stu-id="40cf5-498">Customize the user</span></span>

<span data-ttu-id="40cf5-499">Os usuários associados ao aplicativo podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="40cf5-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="40cf5-500">No exemplo a seguir, todos os usuários autenticados recebem uma `amr` declaração para cada um dos métodos de autenticação do usuário.</span><span class="sxs-lookup"><span data-stu-id="40cf5-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="40cf5-501">Crie uma classe que estenda a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe:</span><span class="sxs-lookup"><span data-stu-id="40cf5-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="40cf5-502">Crie uma fábrica que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="40cf5-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="40cf5-503">Registre o `CustomAccountFactory` para o provedor de autenticação em uso.</span><span class="sxs-lookup"><span data-stu-id="40cf5-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="40cf5-504">Qualquer um dos seguintes registros é válido:</span><span class="sxs-lookup"><span data-stu-id="40cf5-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="40cf5-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="40cf5-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="40cf5-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="40cf5-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="40cf5-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="40cf5-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="40cf5-508">Suporte ao pré-processamento com autenticação</span><span class="sxs-lookup"><span data-stu-id="40cf5-508">Support prerendering with authentication</span></span>

<span data-ttu-id="40cf5-509">Depois de seguir as diretrizes em um dos Blazor Tópicos do aplicativo Webassembly hospedado, use as instruções a seguir para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="40cf5-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="40cf5-510">Processa caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="40cf5-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="40cf5-511">Não PreRender caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="40cf5-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="40cf5-512">Na classe do aplicativo cliente `Program` (*Program.cs*), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="40cf5-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="40cf5-513">No aplicativo do servidor `Startup.ConfigureServices` , registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="40cf5-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="40cf5-514">No método do aplicativo do servidor `Startup.Configure` , substitua os [pontos de extremidade. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) com [pontos de extremidade. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="40cf5-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="40cf5-515">No aplicativo de servidor, crie uma pasta de *páginas* se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="40cf5-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="40cf5-516">Crie uma página *_Host. cshtml* dentro da pasta *Páginas* do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="40cf5-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="40cf5-517">Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo cliente no arquivo *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="40cf5-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="40cf5-518">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="40cf5-518">Update the file's contents:</span></span>

* <span data-ttu-id="40cf5-519">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="40cf5-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="40cf5-520">Substitua a `<app>Loading...</app>` marca pela seguinte:</span><span class="sxs-lookup"><span data-stu-id="40cf5-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="40cf5-521">Opções para aplicativos hospedados e provedores de logon de terceiros</span><span class="sxs-lookup"><span data-stu-id="40cf5-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="40cf5-522">Ao autenticar e autorizar um Blazor aplicativo Webassembly hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="40cf5-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="40cf5-523">O que você escolher dependerá de seu cenário.</span><span class="sxs-lookup"><span data-stu-id="40cf5-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="40cf5-524">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="40cf5-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="40cf5-525">Autenticar usuários para chamar somente APIs de terceiros protegidas</span><span class="sxs-lookup"><span data-stu-id="40cf5-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="40cf5-526">Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="40cf5-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="40cf5-527">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="40cf5-527">In this scenario:</span></span>

* <span data-ttu-id="40cf5-528">O servidor que hospeda o aplicativo não desempenha uma função.</span><span class="sxs-lookup"><span data-stu-id="40cf5-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="40cf5-529">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="40cf5-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="40cf5-530">O aplicativo só pode chamar APIs de terceiros protegidas.</span><span class="sxs-lookup"><span data-stu-id="40cf5-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="40cf5-531">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros</span><span class="sxs-lookup"><span data-stu-id="40cf5-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="40cf5-532">Configure Identity com um provedor de logon de terceiros.</span><span class="sxs-lookup"><span data-stu-id="40cf5-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="40cf5-533">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="40cf5-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="40cf5-534">Quando um usuário faz logon, o Identity coleta tokens de acesso e de atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="40cf5-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="40cf5-535">Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="40cf5-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="40cf5-536">Usar um token de acesso do servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="40cf5-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="40cf5-537">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="40cf5-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="40cf5-538">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente do Identity no cliente.</span><span class="sxs-lookup"><span data-stu-id="40cf5-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="40cf5-539">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="40cf5-539">We don't recommend this approach.</span></span> <span data-ttu-id="40cf5-540">Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="40cf5-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="40cf5-541">Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="40cf5-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="40cf5-542">Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="40cf5-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="40cf5-543">O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="40cf5-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="40cf5-544">Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.</span><span class="sxs-lookup"><span data-stu-id="40cf5-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="40cf5-545">Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="40cf5-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="40cf5-546">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="40cf5-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="40cf5-547">No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="40cf5-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="40cf5-548">Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="40cf5-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="40cf5-549">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="40cf5-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="40cf5-550">O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="40cf5-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="40cf5-551">Usar pontos de extremidade do OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="40cf5-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="40cf5-552">A biblioteca de autenticação e os Blazor modelos usam pontos de extremidade do OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="40cf5-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="40cf5-553">Para usar um ponto de extremidade v 2.0, configure a opção de portador JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="40cf5-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="40cf5-554">No exemplo a seguir, o AAD está configurado para v 2.0 acrescentando um `v2.0` segmento à <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="40cf5-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="40cf5-555">Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="40cf5-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="40cf5-556">Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriedade diretamente.</span><span class="sxs-lookup"><span data-stu-id="40cf5-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="40cf5-557">Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou no arquivo de configurações do aplicativo (*appSettings. JSON*) com a `Authority` chave.</span><span class="sxs-lookup"><span data-stu-id="40cf5-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="40cf5-558">A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0.</span><span class="sxs-lookup"><span data-stu-id="40cf5-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="40cf5-559">Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="40cf5-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
