---
<span data-ttu-id="4eb56-101">Título: ' ASP.NET Core Blazor Webassembly cenários de segurança adicionais ' autor: guardrex Descrição: ' saiba como configurar o Blazor Webassembly para cenários de segurança adicionais '.</span><span class="sxs-lookup"><span data-stu-id="4eb56-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: guardrex description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="4eb56-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 06/01/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="4eb56-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/01/2020 no-loc:</span></span>
- <span data-ttu-id="4eb56-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4eb56-103">'Blazor'</span></span>
- <span data-ttu-id="4eb56-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4eb56-104">'Identity'</span></span>
- <span data-ttu-id="4eb56-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4eb56-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="4eb56-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4eb56-106">'Razor'</span></span>
- <span data-ttu-id="4eb56-107">' SignalR ' UID: Security/mais alto/Webassembly/outros cenários</span><span class="sxs-lookup"><span data-stu-id="4eb56-107">'SignalR' uid: security/blazor/webassembly/additional-scenarios</span></span>

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="4eb56-108">BlazorCenários de segurança adicionais do Webassembly ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4eb56-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="4eb56-109">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4eb56-109">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="4eb56-110">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="4eb56-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="4eb56-111">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> serviço pode ser usado com <xref:System.Net.Http.HttpClient> para anexar tokens de acesso a solicitações de saída.</span><span class="sxs-lookup"><span data-stu-id="4eb56-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="4eb56-112">Os tokens são adquiridos usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> serviço existente.</span><span class="sxs-lookup"><span data-stu-id="4eb56-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="4eb56-113">Se um token não puder ser adquirido, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> será gerado.</span><span class="sxs-lookup"><span data-stu-id="4eb56-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="4eb56-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>tem um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.</span><span class="sxs-lookup"><span data-stu-id="4eb56-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="4eb56-115">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pode ser configurado com URLs autorizadas, escopos e URL de retorno usando <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> o método.</span><span class="sxs-lookup"><span data-stu-id="4eb56-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="4eb56-116">Use uma das abordagens a seguir para configurar um manipulador de mensagens para solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="4eb56-116">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="4eb56-117">[Classe AuthorizationMessageHandler personalizada](#custom-authorizationmessagehandler-class) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="4eb56-117">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="4eb56-118">Configurar o AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="4eb56-118">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="4eb56-119">Classe AuthorizationMessageHandler personalizada</span><span class="sxs-lookup"><span data-stu-id="4eb56-119">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="4eb56-120">No exemplo a seguir, uma classe personalizada estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> que pode ser usada para configurar um <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="4eb56-120">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="4eb56-121">No `Program.Main` (*Program.cs*), um <xref:System.Net.Http.HttpClient> é configurado com o manipulador de mensagem de autorização personalizada:</span><span class="sxs-lookup"><span data-stu-id="4eb56-121">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="4eb56-122">O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="4eb56-122">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="4eb56-123">Em que o cliente é criado com o <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (pacote[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações para a API do servidor:</span><span class="sxs-lookup"><span data-stu-id="4eb56-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="4eb56-124">Configurar o AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="4eb56-124">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="4eb56-125">No exemplo a seguir, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura um <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-125">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="4eb56-126">Para sua conveniência, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> é incluído um que é pré-configurado com o endereço base do aplicativo como uma URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="4eb56-126">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="4eb56-127">Os modelos Webassembly habilitados para autenticação Blazor agora usam <xref:System.Net.Http.IHttpClientFactory> (pacote[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) no projeto de API do servidor para configurar um <xref:System.Net.Http.HttpClient> com o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="4eb56-127">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="4eb56-128">Em que o cliente é criado <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> no exemplo anterior, as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb56-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="4eb56-129">O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o padrão [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="4eb56-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="4eb56-130">HttpClient digitado</span><span class="sxs-lookup"><span data-stu-id="4eb56-130">Typed HttpClient</span></span>

<span data-ttu-id="4eb56-131">Um cliente tipado pode ser definido para lidar com todas as preocupações de aquisição de token e HTTP em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="4eb56-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="4eb56-132">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="4eb56-132">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="4eb56-133">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="4eb56-134">`FetchData`componente (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-134">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="4eb56-135">Configurar o manipulador HttpClient</span><span class="sxs-lookup"><span data-stu-id="4eb56-135">Configure the HttpClient handler</span></span>

<span data-ttu-id="4eb56-136">O manipulador pode ser configurado ainda mais com <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="4eb56-136">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="4eb56-137">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-137">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="4eb56-138">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="4eb56-138">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="4eb56-139">Se o Blazor aplicativo Webassembly normalmente usa um padrão seguro <xref:System.Net.Http.HttpClient> , o aplicativo também pode fazer solicitações de API da Web não autenticadas ou não autorizadas Configurando um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="4eb56-139">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="4eb56-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-140">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="4eb56-141">O registro anterior é além do registro padrão seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="4eb56-141">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="4eb56-142">Um componente cria o <xref:System.Net.Http.HttpClient> do <xref:System.Net.Http.IHttpClientFactory> (pacote[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) para fazer solicitações não autenticadas ou não autorizadas:</span><span class="sxs-lookup"><span data-stu-id="4eb56-142">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="4eb56-143">O controlador na API do servidor, `WeatherForecastNoAuthenticationController` para o exemplo anterior, não está marcado com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="4eb56-143">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="4eb56-144">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="4eb56-144">Request additional access tokens</span></span>

<span data-ttu-id="4eb56-145">Os tokens de acesso podem ser obtidos manualmente chamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="4eb56-145">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="4eb56-146">No exemplo a seguir, Azure Active Directory adicionais (AAD) Microsoft Graph escopos de API são exigidos por um aplicativo para ler dados do usuário e enviar email.</span><span class="sxs-lookup"><span data-stu-id="4eb56-146">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="4eb56-147">Depois de adicionar as permissões de API do Microsoft Graph no portal do Azure AAD, os escopos adicionais são configurados no aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="4eb56-147">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="4eb56-148">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-148">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="4eb56-149">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos.</span><span class="sxs-lookup"><span data-stu-id="4eb56-149">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="4eb56-150">Em um Razor componente:</span><span class="sxs-lookup"><span data-stu-id="4eb56-150">In a Razor component:</span></span>

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

<span data-ttu-id="4eb56-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>apresenta</span><span class="sxs-lookup"><span data-stu-id="4eb56-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="4eb56-152">`true`com o `token` para uso.</span><span class="sxs-lookup"><span data-stu-id="4eb56-152">`true` with the `token` for use.</span></span>
* <span data-ttu-id="4eb56-153">`false`Se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="4eb56-153">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="4eb56-154">HttpClient e HttpRequestMessage com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="4eb56-154">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="4eb56-155">Ao executar em Webassembly em um Blazor aplicativo Webassembly, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> pode ser usado para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="4eb56-155">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="4eb56-156">Por exemplo, você pode especificar o método HTTP e os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="4eb56-156">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="4eb56-157">O componente a seguir faz uma `POST` solicitação para um ponto de extremidade de API de lista de tarefas no servidor e mostra o corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="4eb56-157">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="4eb56-158">A implementação do .NET Webassembly de <xref:System.Net.Http.HttpClient> usa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="4eb56-158">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="4eb56-159">FETCH permite configurar várias [opções específicas de solicitação](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="4eb56-159">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="4eb56-160">As opções de solicitação de busca HTTP podem ser configuradas com <xref:System.Net.Http.HttpRequestMessage> métodos de extensão mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="4eb56-160">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="4eb56-161">Método de extensão</span><span class="sxs-lookup"><span data-stu-id="4eb56-161">Extension method</span></span> | <span data-ttu-id="4eb56-162">Buscar propriedade de solicitação</span><span class="sxs-lookup"><span data-stu-id="4eb56-162">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="4eb56-163">fornecidas</span><span class="sxs-lookup"><span data-stu-id="4eb56-163">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="4eb56-164">armazenar</span><span class="sxs-lookup"><span data-stu-id="4eb56-164">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="4eb56-165">mode</span><span class="sxs-lookup"><span data-stu-id="4eb56-165">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="4eb56-166">verifica</span><span class="sxs-lookup"><span data-stu-id="4eb56-166">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="4eb56-167">Você pode definir opções adicionais usando o método de <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extensão mais genérico.</span><span class="sxs-lookup"><span data-stu-id="4eb56-167">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="4eb56-168">A resposta HTTP normalmente é armazenada em buffer em um Blazor aplicativo Webassembly para habilitar o suporte para leituras de sincronização no conteúdo da resposta.</span><span class="sxs-lookup"><span data-stu-id="4eb56-168">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="4eb56-169">Para habilitar o suporte para streaming de resposta, use o <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> método de extensão na solicitação.</span><span class="sxs-lookup"><span data-stu-id="4eb56-169">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="4eb56-170">Para incluir credenciais em uma solicitação entre origens, use o <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="4eb56-170">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="4eb56-171">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="4eb56-171">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="4eb56-172">Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações CORS, o `Authorization` cabeçalho deve ser permitido pela política CORS.</span><span class="sxs-lookup"><span data-stu-id="4eb56-172">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="4eb56-173">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="4eb56-173">The following policy includes configuration for:</span></span>

* <span data-ttu-id="4eb56-174">Origens da solicitação ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="4eb56-174">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="4eb56-175">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="4eb56-175">Any method (verb).</span></span>
* <span data-ttu-id="4eb56-176">`Content-Type``Authorization`cabeçalhos e.</span><span class="sxs-lookup"><span data-stu-id="4eb56-176">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="4eb56-177">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header` ), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="4eb56-177">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="4eb56-178">Credenciais definidas pelo código JavaScript do lado do cliente ( `credentials` propriedade definida como `include` ).</span><span class="sxs-lookup"><span data-stu-id="4eb56-178">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="4eb56-179">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="4eb56-179">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="4eb56-180">Manipular erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="4eb56-180">Handle token request errors</span></span>

<span data-ttu-id="4eb56-181">Quando um aplicativo de página única (SPA) autentica um usuário usando o OIDC (Open ID Connect), o estado de autenticação é mantido localmente no SPA e no Identity provedor (IP) na forma de um cookie de sessão que é definido como resultado do usuário que fornece suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="4eb56-181">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="4eb56-182">Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="4eb56-182">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="4eb56-183">Caso contrário, o usuário será desconectado após os tokens concedidos expirarem.</span><span class="sxs-lookup"><span data-stu-id="4eb56-183">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="4eb56-184">Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="4eb56-184">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="4eb56-185">Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP.</span><span class="sxs-lookup"><span data-stu-id="4eb56-185">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="4eb56-186">Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="4eb56-186">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="4eb56-187">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.</span><span class="sxs-lookup"><span data-stu-id="4eb56-187">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="4eb56-188">Esses cenários não são específicos para a autenticação baseada em token.</span><span class="sxs-lookup"><span data-stu-id="4eb56-188">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="4eb56-189">Eles fazem parte da natureza do SPAs.</span><span class="sxs-lookup"><span data-stu-id="4eb56-189">They are part of the nature of SPAs.</span></span> <span data-ttu-id="4eb56-190">Um SPA que usa cookies também falha ao chamar uma API de servidor se o cookie de autenticação for removido.</span><span class="sxs-lookup"><span data-stu-id="4eb56-190">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="4eb56-191">Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="4eb56-191">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="4eb56-192">Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="4eb56-192">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="4eb56-193">Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="4eb56-193">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="4eb56-194">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="4eb56-194">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="4eb56-195">A solicitação é realizada com sucesso e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="4eb56-195">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="4eb56-196">A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="4eb56-196">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="4eb56-197">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="4eb56-197">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="4eb56-198">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="4eb56-198">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="4eb56-199">Armazene o estado da página atual no armazenamento de sessão.</span><span class="sxs-lookup"><span data-stu-id="4eb56-199">Store the current page state in session storage.</span></span> <span data-ttu-id="4eb56-200">Durante o [evento de ciclo de vida OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="4eb56-200">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="4eb56-201">Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.</span><span class="sxs-lookup"><span data-stu-id="4eb56-201">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="4eb56-202">Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="4eb56-202">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="4eb56-203">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="4eb56-203">The following example shows how to:</span></span>

* <span data-ttu-id="4eb56-204">Preserve o estado antes de redirecionar para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="4eb56-204">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="4eb56-205">Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="4eb56-205">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="4eb56-206">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="4eb56-206">Save app state before an authentication operation</span></span>

<span data-ttu-id="4eb56-207">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="4eb56-207">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="4eb56-208">Esse pode ser o caso quando você estiver usando algo como um contêiner de estado e desejar restaurar o estado depois que a autenticação for realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="4eb56-208">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="4eb56-209">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="4eb56-209">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="4eb56-210">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="4eb56-211">Personalizar rotas de aplicativo</span><span class="sxs-lookup"><span data-stu-id="4eb56-211">Customize app routes</span></span>

<span data-ttu-id="4eb56-212">Por padrão, a biblioteca [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="4eb56-212">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="4eb56-213">Rota</span><span class="sxs-lookup"><span data-stu-id="4eb56-213">Route</span></span>                            | <span data-ttu-id="4eb56-214">Finalidade</span><span class="sxs-lookup"><span data-stu-id="4eb56-214">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="4eb56-215">Dispara uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="4eb56-215">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="4eb56-216">Manipula o resultado de qualquer operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="4eb56-216">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="4eb56-217">Exibe mensagens de erro quando a operação de entrada falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="4eb56-217">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="4eb56-218">Dispara uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="4eb56-218">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="4eb56-219">Lida com o resultado de uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="4eb56-219">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="4eb56-220">Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="4eb56-220">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="4eb56-221">Indica que o usuário fez logoff com êxito.</span><span class="sxs-lookup"><span data-stu-id="4eb56-221">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="4eb56-222">Dispara uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="4eb56-222">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="4eb56-223">Dispara uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="4eb56-223">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="4eb56-224">As rotas mostradas na tabela anterior são configuráveis via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4eb56-224">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="4eb56-225">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.</span><span class="sxs-lookup"><span data-stu-id="4eb56-225">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="4eb56-226">No exemplo a seguir, todos os caminhos são prefixados com `/security` .</span><span class="sxs-lookup"><span data-stu-id="4eb56-226">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="4eb56-227">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-227">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="4eb56-228">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-228">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="4eb56-229">Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> com um parâmetro de ação explícita:</span><span class="sxs-lookup"><span data-stu-id="4eb56-229">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="4eb56-230">Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.</span><span class="sxs-lookup"><span data-stu-id="4eb56-230">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="4eb56-231">Personalizar a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="4eb56-231">Customize the authentication user interface</span></span>

<span data-ttu-id="4eb56-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4eb56-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="4eb56-233">Cada Estado pode ser personalizado passando um personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="4eb56-233">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="4eb56-234">Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o da <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="4eb56-234">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="4eb56-235">`Authentication`componente (*páginas/autenticação. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="4eb56-236">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="4eb56-236">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="4eb56-237">Rota</span><span class="sxs-lookup"><span data-stu-id="4eb56-237">Route</span></span>                            | <span data-ttu-id="4eb56-238">Fragmento</span><span class="sxs-lookup"><span data-stu-id="4eb56-238">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="4eb56-239">Personalizar o usuário</span><span class="sxs-lookup"><span data-stu-id="4eb56-239">Customize the user</span></span>

<span data-ttu-id="4eb56-240">Os usuários associados ao aplicativo podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="4eb56-240">Users bound to the app can be customized.</span></span> <span data-ttu-id="4eb56-241">No exemplo a seguir, todos os usuários autenticados recebem uma `amr` declaração para cada um dos métodos de autenticação do usuário.</span><span class="sxs-lookup"><span data-stu-id="4eb56-241">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="4eb56-242">Crie uma classe que estenda a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe:</span><span class="sxs-lookup"><span data-stu-id="4eb56-242">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="4eb56-243">Crie uma fábrica que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="4eb56-243">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="4eb56-244">Registre o `CustomAccountFactory` para o provedor de autenticação em uso.</span><span class="sxs-lookup"><span data-stu-id="4eb56-244">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="4eb56-245">Qualquer um dos seguintes registros é válido:</span><span class="sxs-lookup"><span data-stu-id="4eb56-245">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="4eb56-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="4eb56-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="4eb56-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="4eb56-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="4eb56-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="4eb56-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="4eb56-249">Suporte ao pré-processamento com autenticação</span><span class="sxs-lookup"><span data-stu-id="4eb56-249">Support prerendering with authentication</span></span>

<span data-ttu-id="4eb56-250">Depois de seguir as diretrizes em um dos Blazor Tópicos do aplicativo Webassembly hospedado, use as instruções a seguir para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="4eb56-250">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="4eb56-251">Processa caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="4eb56-251">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="4eb56-252">Não PreRender caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="4eb56-252">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="4eb56-253">Na classe do aplicativo cliente `Program` (*Program.cs*), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="4eb56-253">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="4eb56-254">No aplicativo do servidor `Startup.ConfigureServices` , registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="4eb56-254">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="4eb56-255">No método do aplicativo do servidor `Startup.Configure` , substitua os [pontos de extremidade. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) com [pontos de extremidade. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="4eb56-255">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="4eb56-256">No aplicativo de servidor, crie uma pasta de *páginas* se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="4eb56-256">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="4eb56-257">Crie uma página *_Host. cshtml* dentro da pasta *Páginas* do aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb56-257">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="4eb56-258">Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo cliente no arquivo *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4eb56-258">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="4eb56-259">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="4eb56-259">Update the file's contents:</span></span>

* <span data-ttu-id="4eb56-260">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4eb56-260">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="4eb56-261">Substitua a `<app>Loading...</app>` marca pela seguinte:</span><span class="sxs-lookup"><span data-stu-id="4eb56-261">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="4eb56-262">Opções para aplicativos hospedados e provedores de logon de terceiros</span><span class="sxs-lookup"><span data-stu-id="4eb56-262">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="4eb56-263">Ao autenticar e autorizar um Blazor aplicativo Webassembly hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="4eb56-263">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="4eb56-264">O que você escolher dependerá de seu cenário.</span><span class="sxs-lookup"><span data-stu-id="4eb56-264">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="4eb56-265">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="4eb56-265">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="4eb56-266">Autenticar usuários para chamar somente APIs de terceiros protegidas</span><span class="sxs-lookup"><span data-stu-id="4eb56-266">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="4eb56-267">Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="4eb56-267">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="4eb56-268">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="4eb56-268">In this scenario:</span></span>

* <span data-ttu-id="4eb56-269">O servidor que hospeda o aplicativo não desempenha uma função.</span><span class="sxs-lookup"><span data-stu-id="4eb56-269">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="4eb56-270">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="4eb56-270">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="4eb56-271">O aplicativo só pode chamar APIs de terceiros protegidas.</span><span class="sxs-lookup"><span data-stu-id="4eb56-271">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="4eb56-272">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros</span><span class="sxs-lookup"><span data-stu-id="4eb56-272">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="4eb56-273">Configure Identity com um provedor de logon de terceiros.</span><span class="sxs-lookup"><span data-stu-id="4eb56-273">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="4eb56-274">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="4eb56-274">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="4eb56-275">Quando um usuário faz logon, o Identity coleta tokens de acesso e de atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4eb56-275">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="4eb56-276">Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="4eb56-276">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="4eb56-277">Usar um token de acesso do servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="4eb56-277">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="4eb56-278">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb56-278">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="4eb56-279">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente do Identity no cliente.</span><span class="sxs-lookup"><span data-stu-id="4eb56-279">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="4eb56-280">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="4eb56-280">We don't recommend this approach.</span></span> <span data-ttu-id="4eb56-281">Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="4eb56-281">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="4eb56-282">Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="4eb56-282">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="4eb56-283">Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="4eb56-283">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="4eb56-284">O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="4eb56-284">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="4eb56-285">Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.</span><span class="sxs-lookup"><span data-stu-id="4eb56-285">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="4eb56-286">Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="4eb56-286">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="4eb56-287">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="4eb56-287">Make an API call from the client to the server API.</span></span> <span data-ttu-id="4eb56-288">No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="4eb56-288">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="4eb56-289">Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="4eb56-289">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="4eb56-290">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="4eb56-290">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="4eb56-291">O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="4eb56-291">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="4eb56-292">Usar pontos de extremidade do OIDC (Open ID Connect) v 2.0</span><span class="sxs-lookup"><span data-stu-id="4eb56-292">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="4eb56-293">A biblioteca de autenticação e os Blazor modelos usam pontos de extremidade do OIDC (Open ID Connect) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="4eb56-293">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="4eb56-294">Para usar um ponto de extremidade v 2.0, configure a opção de portador JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4eb56-294">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="4eb56-295">No exemplo a seguir, o AAD está configurado para v 2.0 acrescentando um `v2.0` segmento à <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="4eb56-295">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="4eb56-296">Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="4eb56-296">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="4eb56-297">Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriedade diretamente.</span><span class="sxs-lookup"><span data-stu-id="4eb56-297">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="4eb56-298">Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou no arquivo de configurações do aplicativo (*appSettings. JSON*) com a `Authority` chave.</span><span class="sxs-lookup"><span data-stu-id="4eb56-298">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="4eb56-299">A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0.</span><span class="sxs-lookup"><span data-stu-id="4eb56-299">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="4eb56-300">Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="4eb56-300">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
