---
title: Cenários Blazor de segurança adicionais do webassembly ASP.NET Core
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 2dbb2bbd07c427c594a12b8037f35cfff2228191
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111169"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Cenários de segurança adicionais do Webassembly ASP.NET Core mais incrivelmente

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> As diretrizes neste artigo se aplicam a ASP.NET Core 3,2 Preview 4. Este tópico será atualizado para cobrir a visualização 5 em sexta-feira, 24 de abril.

## <a name="request-additional-access-tokens"></a>Solicitar tokens de acesso adicionais

A maioria dos aplicativos requer apenas um token de acesso para interagir com os recursos protegidos que eles usam. Em alguns cenários, um aplicativo pode exigir mais de um token para interagir com dois ou mais recursos.

No exemplo a seguir, Azure Active Directory adicionais (AAD) Microsoft Graph escopos de API são exigidos por um aplicativo para ler dados do usuário e enviar email. Depois de adicionar as permissões de API do Microsoft Graph no portal do Azure AAD, os escopos adicionais são configurados no aplicativo cliente (`Program.Main`, *Program.cs*):

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

O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token com um determinado conjunto de escopos, como visto no exemplo a seguir:

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

`TryGetToken`apresenta

* `true`com o `token` para uso.
* `false`Se o token não for recuperado.

## <a name="attach-tokens-to-outgoing-requests"></a>Anexar tokens a solicitações de saída

O `AuthorizationMessageHandler` serviço pode ser usado com `HttpClient` para anexar tokens de acesso a solicitações de saída. Os tokens são adquiridos `IAccessTokenProvider` usando o serviço existente. Se um token não puder ser adquirido `AccessTokenNotAvailableException` , um será gerado. `AccessTokenNotAvailableException`tem um `Redirect` método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token. O `AuthorizationMessageHandler` pode ser configurado com URLs autorizadas, escopos e URL de retorno usando `ConfigureHandler` o método.

No exemplo a seguir, `AuthorizationMessageHandler` configura um `HttpClient` in `Program.Main` (*Program.cs*):

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

Para sua conveniência, `BaseAddressAuthorizationMessageHandler` é incluído um que é pré-configurado com o endereço base do aplicativo como uma URL autorizada. Os modelos Webassembly do mais alto-habilitado para autenticação agora usam [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) para configurar `HttpClient` um com `BaseAddressAuthorizationMessageHandler`o:

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

Em que o cliente é criado `CreateClient` no exemplo anterior, as instâncias `HttpClient` do são fornecidas que incluem tokens de acesso ao fazer solicitações ao projeto do servidor.

Em seguida `HttpClient` , o configurado é usado para fazer solicitações autorizadas `try-catch` usando um padrão simples. O componente `FetchData` a seguir solicita dados de previsão do tempo:

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

Como alternativa, você pode definir um cliente tipado que lide com todas as questões de aquisição de token e HTTP em uma única classe:

*WeatherClient.cs*:

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

*Program.cs*:

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*FetchData. Razor*:

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a>Manipular erros de solicitação de token

Quando um aplicativo de página única (SPA) autentica um usuário usando o OIDC (Open ID Connect), o estado de autenticação é mantido localmente no SPA e no provedor de identidade (IP) na forma de um cookie de sessão que é definido como resultado do usuário que fornece suas credenciais.

Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens. Caso contrário, o usuário será desconectado após os tokens concedidos expirarem. Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.

Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP. Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido. Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.

Esses cenários não são específicos para a autenticação baseada em token. Eles fazem parte da natureza do SPAs. Um SPA que usa cookies também falha ao chamar uma API de servidor se o cookie de autenticação for removido.

Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:

* Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.
* Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.

Quando o aplicativo solicita um token, há dois resultados possíveis:

* A solicitação é realizada com sucesso e o aplicativo tem um token válido.
* A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.

Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento. Existem várias abordagens com níveis crescentes de complexidade:

* Armazene o estado da página atual no armazenamento de sessão. Durante `OnInitializeAsync`, verifique se o estado pode ser restaurado antes de continuar.
* Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.
* Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.

O exemplo a seguir mostra como:

* Preserve o estado antes de redirecionar para a página de logon.
* Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Salvar o estado do aplicativo antes de uma operação de autenticação

Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP. Esse pode ser o caso quando você estiver usando algo como um contêiner de estado e desejar restaurar o estado depois que a autenticação for realizada com sucesso. Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.

`Authentication`componente (*páginas/autenticação. Razor*):

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

## <a name="customize-app-routes"></a>Personalizar rotas de aplicativo

Por padrão, a `Microsoft.AspNetCore.Components.WebAssembly.Authentication` biblioteca usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.

| Rota                            | Finalidade |
| -------------------------------- | ------- |
| `authentication/login`           | Dispara uma operação de entrada. |
| `authentication/login-callback`  | Manipula o resultado de qualquer operação de entrada. |
| `authentication/login-failed`    | Exibe mensagens de erro quando a operação de entrada falha por algum motivo. |
| `authentication/logout`          | Dispara uma operação de saída. |
| `authentication/logout-callback` | Lida com o resultado de uma operação de saída. |
| `authentication/logout-failed`   | Exibe mensagens de erro quando a operação de saída falha por algum motivo. |
| `authentication/logged-out`      | Indica que o usuário fez logoff com êxito. |
| `authentication/profile`         | Dispara uma operação para editar o perfil do usuário. |
| `authentication/register`        | Dispara uma operação para registrar um novo usuário. |

As rotas mostradas na tabela anterior são configuráveis `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`via. Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.

No exemplo a seguir, todos os caminhos são prefixados `/security`com.

`Authentication`componente (*páginas/autenticação. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o `RemoteAuthenticatorView` com um parâmetro de ação explícita:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.

## <a name="customize-the-authentication-user-interface"></a>Personalizar a interface do usuário de autenticação

`RemoteAuthenticatorView`inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação. Cada Estado pode ser personalizado passando um personalizado `RenderFragment`. Para personalizar o texto exibido durante o processo de logon inicial, o pode `RemoteAuthenticatorView` alterar o da seguinte maneira.

`Authentication`componente (*páginas/autenticação. Razor*):

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

O `RemoteAuthenticatorView` tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.

| Rota                            | Fragmento                |
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

## <a name="support-prerendering-with-authentication"></a>Suporte ao pré-processamento com autenticação

Depois de seguir as diretrizes em um dos tópicos Blazor do aplicativo Webassembly hospedado, use as instruções a seguir para criar um aplicativo que:

* Processa caminhos para os quais a autorização não é necessária.
* Não PreRender caminhos para os quais a autorização é necessária.

Na classe do `Program` aplicativo cliente (*Program.cs*), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices`):

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

No aplicativo do `Startup.ConfigureServices`servidor, registre os seguintes serviços adicionais:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

No método do aplicativo do `Startup.Configure` servidor, substitua `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

No aplicativo de servidor, crie uma pasta de *páginas* se ela não existir. Crie uma página *_Host. cshtml* dentro da pasta *Páginas* do aplicativo do servidor. Cole o conteúdo do arquivo *wwwroot/index.html* do aplicativo cliente no arquivo *pages/_Host. cshtml* . Atualize o conteúdo do arquivo:

* Adicione `@page "_Host"` ao topo do arquivo.
* Substitua a `<app>Loading...</app>` marca pela seguinte:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opções para aplicativos hospedados e provedores de logon de terceiros

Ao autenticar e autorizar um Blazor aplicativo Webassembly hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário. O que você escolher dependerá de seu cenário.

Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticar usuários para chamar somente APIs de terceiros protegidas

Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Neste cenário:

* O servidor que hospeda o aplicativo não desempenha uma função.
* As APIs no servidor não podem ser protegidas.
* O aplicativo só pode chamar APIs de terceiros protegidas.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros

Configure a identidade com um provedor de logon de terceiros. Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.

Quando um usuário faz logon, a identidade coleta tokens de acesso e de atualização como parte do processo de autenticação. Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Usar um token de acesso do servidor para recuperar o token de acesso de terceiros

Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor. A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente da identidade no cliente.

Não recomendamos essa abordagem. Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público. Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial. Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.

* O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.
* Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros

Faça uma chamada de API do cliente para a API do servidor. No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.

Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:

* O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.
* O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.
