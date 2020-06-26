---
title: BlazorAutenticação e autorização do ASP.NET Core
author: guardrex
description: Saiba mais sobre os Blazor cenários de autenticação e autorização.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/index
ms.openlocfilehash: e905f08f867b73fc37d5fed7138256ac89811312
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402397"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>BlazorAutenticação e autorização do ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)

O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em Blazor aplicativos.

Cenários de segurança diferem entre Blazor Server Blazor WebAssembly aplicativos e. Como os Blazor Server aplicativos são executados no servidor, as verificações de autorização são capazes de determinar:

* As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).
* As regras de acesso para áreas do aplicativo e componentes.

Blazor WebAssemblyos aplicativos são executados no cliente. A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas. Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um Blazor WebAssembly aplicativo não pode impor regras de acesso de autorização.

As [ Razor convenções de autorização de páginas](xref:security/authorization/razor-pages-authorization) não se aplicam a componentes roteáveis Razor . Se um componente não roteável Razor for [inserido em uma página](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), as convenções de autorização da página afetarão indiretamente o Razor componente junto com o restante do conteúdo da página.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> não têm suporte em Razor componentes do.

## <a name="authentication"></a>Autenticação

Blazorusa os mecanismos de autenticação de ASP.NET Core existentes para estabelecer a identidade do usuário. O mecanismo exato depende de como o Blazor aplicativo é hospedado Blazor WebAssembly ou Blazor Server .

### <a name="blazor-webassembly-authentication"></a>autenticação Blazor WebAssembly

Em Blazor WebAssembly aplicativos, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

Adicione o seguinte:

* Uma referência de pacote para [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) o arquivo de projeto do aplicativo.
* O `Microsoft.AspNetCore.Components.Authorization` namespace para o arquivo do aplicativo `_Imports.razor` .

Para lidar com a autenticação, a implementação de um serviço interno ou personalizado <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> é abordada nas seções a seguir.

Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:blazor/security/webassembly/index> .

### <a name="blazor-server-authentication"></a>autenticação Blazor Server

Blazor Serveros aplicativos operam em uma conexão em tempo real que é criada usando o SignalR . A [autenticação em SignalR aplicativos baseados no](xref:signalr/authn-and-authz) é tratada quando a conexão é estabelecida. A autenticação pode ser baseada em um cookie ou um algum outro token de portador.

Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:blazor/security/server/index> .

## <a name="authenticationstateprovider-service"></a>Serviço AuthenticationStateProvider

O <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> serviço interno obtém dados de estado de autenticação do ASP.NET Core `HttpContext.User` . É assim que o estado de autenticação se integra aos mecanismos existentes de autenticação de ASP.NET Core.

O <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> é o serviço subjacente usado pelos componentes <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> e <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> para obter o estado de autenticação.

Normalmente, você não usa o <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> diretamente. Use o [ `AuthorizeView` componente](#authorizeview-component) ou [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) abordagens descritas posteriormente neste artigo. A principal desvantagem de usar o <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.

O serviço <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Se `user.Identity.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.

Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/fundamentals/dependency-injection> e <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementar um AuthenticationStateProvider personalizado

Se o aplicativo exigir um provedor personalizado, implemente <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> e substitua `GetAuthenticationStateAsync` :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

Em um Blazor WebAssembly aplicativo, o `CustomAuthStateProvider` serviço é registrado em `Main` `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Em um Blazor Server aplicativo, o `CustomAuthStateProvider` serviço é registrado em `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Usando o `CustomAuthStateProvider` no exemplo anterior, todos os usuários são autenticados com o nome de usuário `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Expor o estado de autenticação como um parâmetro em cascata

Se os dados de estado de autenticação forem necessários para a lógica de procedimento, como ao executar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.

Configure o `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parâmetro em cascata usando os <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componentes e <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> no `App` componente ( `App.razor` ):

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

Em um Blazor WebAssembly aplicativo, adicione serviços para opções e autorização para `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Em um Blazor Server aplicativo, os serviços para opções e autorização já estão presentes, portanto, nenhuma ação adicional é necessária.

## <a name="authorization"></a>Autorização

Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.

O acesso geralmente é concedido ou negado com base nos seguintes casos:

* Se o usuário está autenticado (conectado).
* Se o usuário está em uma *função*.
* Se o usuário tem uma *declaração*.
* Se uma *política* é atendida.

Cada um desses conceitos é o mesmo de um aplicativo ASP.NET Core MVC ou de Razor páginas. Para obter mais informações sobre ASP.NET Core segurança, consulte os artigos em [ASP.NET Core segurança Identity e ](xref:security/index).

## <a name="authorizeview-component"></a>Componente AuthorizeView

O componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la. Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.

O componente expõe uma variável `context` do tipo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, que pode ser usada para acessar informações sobre o usuário conectado:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

O <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> componente pode ser usado no `NavMenu` componente ( `Shared/NavMenu.razor` ) para exibir um item de lista ( `<li>...</li>` ) para um [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) () <xref:Microsoft.AspNetCore.Components.Routing.NavLink> , mas observe que essa abordagem apenas remove o item de lista da saída renderizada. Ele não impede que o usuário navegue até o componente.

O conteúdo de `<Authorized>` `<NotAuthorized>` marcas e pode incluir itens arbitrários, como outros componentes interativos.

As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).

Se as condições de autorização não forem especificadas, o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usará uma política padrão e tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

### <a name="role-based-and-policy-based-authorization"></a>Autorização baseada em funções e em políticas

O componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> dá suporte à autorização *baseada em funções* ou *baseada em políticas*.

Para a autorização baseada em funções, use o parâmetro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Para obter mais informações, consulte <xref:security/authorization/roles>.

Para a autorização baseada em políticas, use o parâmetro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

A autorização baseada em declarações é um caso especial de autorização baseada em políticas. Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração. Para obter mais informações, consulte <xref:security/authorization/policies>.

Essas APIs podem ser usadas em um Blazor Server ou em Blazor WebAssembly aplicativos.

Se <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> e <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> não forem especificados, o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usará a política padrão.

### <a name="content-displayed-during-asynchronous-authentication"></a>Conteúdo exibido durante a autenticação assíncrona

Blazorpermite que o estado de autenticação seja determinado de *forma assíncrona*. O cenário principal para essa abordagem é em Blazor WebAssembly aplicativos que fazem uma solicitação para um ponto de extremidade externo para autenticação.

Enquanto a autenticação estiver em andamento, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> não exibirá nenhum conteúdo por padrão. Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Essa abordagem não é normalmente aplicável a Blazor Server aplicativos. Blazor Serveros aplicativos conhecem o estado de autenticação assim que o estado é estabelecido. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>o conteúdo pode ser fornecido no Blazor Server componente de um aplicativo <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , mas o conteúdo nunca é exibido.

## <a name="authorize-attribute"></a>Atributo [Authorize]

O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo pode ser usado em Razor componentes:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Somente [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o uso em `@page` componentes foi atingido por meio do Blazor roteador. A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página. Para autorizar a exibição de partes específicas dentro de uma página, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.

O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo também oferece suporte à autorização baseada em função ou em políticas. Para a autorização baseada em funções, use o parâmetro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Para a autorização baseada em políticas, use o parâmetro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Se nem <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nem <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> for especificado, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) o usará a política padrão, que por padrão é tratar:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personalizar conteúdo não autorizado com o componente Router

O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, em conjunto com o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente, permite que o aplicativo especifique o conteúdo personalizado se:

* O conteúdo não for encontrado.
* O usuário falha [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) em uma condição aplicada ao componente. O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo é abordado na seção [ `[Authorize]` atributo](#authorize-attribute) .
* A autenticação assíncrona estiver em andamento.

No modelo de Blazor Server projeto padrão, o `App` componente ( `App.razor` ) demonstra como definir o conteúdo personalizado:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

O conteúdo das `<NotFound>` `<NotAuthorized>` marcas, e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.

Se o `<NotAuthorized>` elemento não for especificado, o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> usará a seguinte mensagem de fallback:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notificação sobre mudanças no estado de autenticação

Se o aplicativo determinar que os dados de estado de autenticação subjacentes foram alterados (por exemplo, porque o usuário se desconectou ou outro usuário alterou suas funções), um [personalizado `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) poderá invocar opcionalmente o método <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> na <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> classe base. Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) para realizar uma nova renderização usando os novos dados.

## <a name="procedural-logic"></a>Lógica de procedimento

Se o aplicativo for necessário para verificar as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` para obter o usuário <xref:System.Security.Claims.ClaimsPrincipal> . `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`pode ser combinado com outros serviços, como `IAuthorizationService` , para avaliar políticas.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Em um Blazor WebAssembly componente de aplicativo, adicione <xref:Microsoft.AspNetCore.Authorization> os <xref:Microsoft.AspNetCore.Components.Authorization> namespaces e:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Esses namespaces podem ser fornecidos globalmente adicionando-os ao arquivo do aplicativo `_Imports.razor` .

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Erros comuns:

* **A autorização requer um parâmetro em cascata do tipo `Task\<AuthenticationState>` . Considere usar `CascadingAuthenticationState` para fornecer isso.**

* **`null`o valor é recebido para`authenticationStateTask`**

É provável que o projeto não tenha sido criado usando um Blazor Server modelo com autenticação habilitada. Empacote uma `<CascadingAuthenticationState>` parte da árvore de interface do usuário, por exemplo, no `App` componente ( `App.razor` ) da seguinte maneira:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

O <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> fornece o `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parâmetro em cascata, que, por sua vez, recebe do <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> serviço de injeção subjacente.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Incrível Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) links de exemplo da comunidade de autenticação
