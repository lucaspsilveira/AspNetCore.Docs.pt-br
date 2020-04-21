<span data-ttu-id="ca7ad-101">O `FetchData` componente mostra como:</span><span class="sxs-lookup"><span data-stu-id="ca7ad-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="ca7ad-102">Provisão um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-102">Provision an access token.</span></span>
* <span data-ttu-id="ca7ad-103">Use o token de acesso para chamar uma API de recurso protegido no aplicativo *Server.*</span><span class="sxs-lookup"><span data-stu-id="ca7ad-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="ca7ad-104">A `@attribute [Authorize]` diretiva indica ao sistema de autorização Blazor WebAssembly que o usuário deve ser autorizado a visitar este componente.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="ca7ad-105">A presença do atributo no aplicativo *Cliente* não impede que a API no servidor seja chamada sem credenciais adequadas.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="ca7ad-106">O aplicativo *Server* `[Authorize]` também deve usar nos pontos finais apropriados para protegê-los corretamente.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="ca7ad-107">`AuthenticationService.RequestAccessToken();`cuida de solicitar um token de acesso que pode ser adicionado à solicitação para chamar a API.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="ca7ad-108">Se o token estiver em cache ou o serviço for capaz de provisionar um novo token de acesso sem interação do usuário, a solicitação do token será bem sucedida.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="ca7ad-109">Caso contrário, a solicitação de token falha.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="ca7ad-110">Para obter o token real para incluir na solicitação, o aplicativo deve `tokenResult.TryGetToken(out var token)`verificar se a solicitação foi bem sucedida por meio da chamada .</span><span class="sxs-lookup"><span data-stu-id="ca7ad-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="ca7ad-111">Se a solicitação foi bem sucedida, a variável token será preenchida com o token de acesso.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="ca7ad-112">A `Value` propriedade do token expõe a seqüência literal a ser inscisa na cabeçalho de `Authorization` solicitação.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="ca7ad-113">Se a solicitação falhou porque o token não pôde ser provisionado sem interação do usuário, o resultado do token contém uma URL redirecionada.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="ca7ad-114">Navegar para esta URL leva o usuário à página de login e volta para a página atual após uma autenticação bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="ca7ad-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="ca7ad-115">Para obter mais informações, consulte [Salvar o estado do aplicativo antes de uma operação de autenticação](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="ca7ad-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
