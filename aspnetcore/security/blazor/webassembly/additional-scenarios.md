---
title: ASP.NET Blazor principais cenários de segurança do Core WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661588"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly cenários adicionais de segurança

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Solicite tokens de acesso adicionais

A maioria dos aplicativos só requer um token de acesso para interagir com os recursos protegidos que eles usam. Em alguns cenários, um aplicativo pode exigir mais de um token para interagir com dois ou mais recursos.

No exemplo a seguir, outros escopos de AAD (AAD) do Microsoft Graph AaD são exigidos por um aplicativo para ler dados do usuário e enviar e-mails. Depois de adicionar as permissões da API do Microsoft Graph no portal Azure AAD, os escopos adicionais são configurados no aplicativo Client (`Program.Main` *Program.cs*):

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

O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo forneça um token com um determinado conjunto de escopos, como visto no exemplo a seguir:

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

`TryGetToken`Retorna:

* `true`com `token` o para uso.
* `false`se o token não for recuperado.

## <a name="handle-token-request-errors"></a>Lidar com erros de solicitação de token

Quando um SPA (Single Page Application, aplicativo de página única) autentica um usuário usando o Open ID Connect (OIDC), o estado de autenticação é mantido localmente dentro do SPA e no Provedor de Identidade (IP) na forma de um cookie de sessão definido como resultado do usuário fornecer suas credenciais.

Os tokens que o IP emite para o usuário normalmente são válidos por curtos períodos de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens. Caso contrário, o usuário seria logado após o vencimento dos tokens concedidos. Na maioria dos casos, os clientes da OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.

Existem alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logon explicitamente fora do IP. Esse cenário ocorre se `https://login.microsoftonline.com` um usuário visitar e fazer loga. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logout. Qualquer token que o cliente possui pode não ser mais válido. Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário após o término do token atual.

Esses cenários não são específicos para autenticação baseada em tokens. Eles fazem parte da natureza dos SPAs. Um SPA usando cookies também falha em chamar uma API de servidor se o cookie de autenticação for removido.

Quando um aplicativo executa chamadas de API para recursos protegidos, você deve estar ciente do seguinte:

* Para prover um novo token de acesso para chamar a API, o usuário pode ser obrigado a autenticar novamente.
* Mesmo que o cliente tenha um token que parece ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.

Quando o aplicativo solicita um token, há dois resultados possíveis:

* A solicitação é bem sucedida, e o aplicativo tem um token válido.
* A solicitação falha, e o aplicativo deve autenticar o usuário novamente para obter um novo token.

Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento. Existem várias abordagens com níveis crescentes de complexidade:

* Armazene o estado atual da página no armazenamento da sessão. Durante `OnInitializeAsync`, verifique se o estado pode ser restaurado antes de continuar.
* Adicione um parâmetro de seqüência de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa para reidratar o estado previamente salvo.
* Adicione um parâmetro de seqüência de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem correr o risco de colisões com outros itens.

O exemplo a seguir mostra como:

* Preserve o estado antes de redirecionar para a página de login.
* Recupere o estado anterior após a autenticação usando o parâmetro de seqüência de string de consulta.

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

Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP. Isso pode ser o caso quando você está usando algo como um contêiner de estado e você quer restaurar o estado após o sucesso da autenticação. Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado assim que a operação de autenticação for concluída com sucesso.

`Authentication`componente *(Páginas/Autenticação.navalha):*

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

## <a name="customize-app-routes"></a>Personalizar rotas de aplicativos

Por padrão, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` a biblioteca usa as rotas mostradas na tabela a seguir para representar diferentes estados de autenticação.

| Rota                            | Finalidade |
| -------------------------------- | ------- |
| `authentication/login`           | Desencadeia uma operação de entrada. |
| `authentication/login-callback`  | Lida com o resultado de qualquer operação de login. |
| `authentication/login-failed`    | Exibe mensagens de erro quando a operação de login falha por algum motivo. |
| `authentication/logout`          | Desencadeia uma operação de saída. |
| `authentication/logout-callback` | Lida com o resultado de uma operação de sinalização. |
| `authentication/logout-failed`   | Exibe mensagens de erro quando a operação de saída falha por algum motivo. |
| `authentication/logged-out`      | Indica que o usuário fez logout com sucesso. |
| `authentication/profile`         | Aciona uma operação para editar o perfil do usuário. |
| `authentication/register`        | Desencadeia uma operação para registrar um novo usuário. |

As rotas mostradas na tabela `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`anterior são configuráveis via . Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que lida com cada caminho.

No exemplo a seguir, todos os `/security`caminhos são prefixados com .

`Authentication`componente *(Páginas/Autenticação.navalha):*

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`*(Program.cs):*

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

Se a exigência exige caminhos completamente diferentes, defina `RemoteAuthenticatorView` as rotas como descrito anteriormente e torne-as com um parâmetro de ação explícito:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Você pode dividir a ui em páginas diferentes se você optar por fazê-lo.

## <a name="customize-the-authentication-user-interface"></a>Personalize a interface do usuário de autenticação

`RemoteAuthenticatorView`inclui um conjunto padrão de peças de iu para cada estado de autenticação. Cada estado pode ser personalizado passando `RenderFragment`em um costume . Para personalizar o texto exibido durante o processo `RemoteAuthenticatorView` inicial de login, pode alterar o seguinte.

`Authentication`componente *(Páginas/Autenticação.navalha):*

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
