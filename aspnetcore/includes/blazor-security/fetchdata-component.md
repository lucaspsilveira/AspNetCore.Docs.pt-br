O `FetchData` componente mostra como:

* Provisão um token de acesso.
* Use o token de acesso para chamar uma API de recurso protegido no aplicativo *Server.*

A `@attribute [Authorize]` diretiva indica ao sistema de autorização Blazor WebAssembly que o usuário deve ser autorizado a visitar este componente. A presença do atributo no aplicativo *Cliente* não impede que a API no servidor seja chamada sem credenciais adequadas. O aplicativo *Server* `[Authorize]` também deve usar nos pontos finais apropriados para protegê-los corretamente.

`AuthenticationService.RequestAccessToken();`cuida de solicitar um token de acesso que pode ser adicionado à solicitação para chamar a API. Se o token estiver em cache ou o serviço for capaz de provisionar um novo token de acesso sem interação do usuário, a solicitação do token será bem sucedida. Caso contrário, a solicitação de token falha.

Para obter o token real para incluir na solicitação, o aplicativo deve `tokenResult.TryGetToken(out var token)`verificar se a solicitação foi bem sucedida por meio da chamada . 

Se a solicitação foi bem sucedida, a variável token será preenchida com o token de acesso. A `Value` propriedade do token expõe a seqüência literal a ser inscisa na cabeçalho de `Authorization` solicitação.

Se a solicitação falhou porque o token não pôde ser provisionado sem interação do usuário, o resultado do token contém uma URL redirecionada. Navegar para esta URL leva o usuário à página de login e volta para a página atual após uma autenticação bem-sucedida.

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
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

Para obter mais informações, consulte [Salvar o estado do aplicativo antes de uma operação de autenticação](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
