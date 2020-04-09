---
title: autenticação Blazor e autorização do Núcleo ASP.NET
author: guardrex
description: Conheça Blazor os cenários de autenticação e autorização.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501318"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>autenticação Blazor e autorização do Núcleo ASP.NET

Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Para a orientação deste artigo Blazor que se aplica Blazor ao WebAssembly, é necessário o modelo do ASP.NET Core WebAssembly versão 3.2 ou posterior. Se você não estiver usando a versão 16.6 Preview Blazor 2 ou posterior do <xref:blazor/get-started>Visual Studio, obtenha o modelo mais recente do WebAssembly seguindo a orientação em .

ASP.NET Core suporta a configuração Blazor e o gerenciamento de segurança em aplicativos.

Os cenários Blazor de Blazor segurança diferem entre aplicativos Server e WebAssembly. Como Blazor os aplicativos do Servidor são executados no servidor, as verificações de autorização são capazes de determinar:

* As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).
* As regras de acesso para áreas do aplicativo e componentes.

BlazorOs aplicativos WebAssembly são executados no cliente. A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas. Uma vez que as verificações do lado do Blazor cliente podem ser modificadas ou ignoradas por um usuário, um aplicativo webassembly não pode impor regras de acesso à autorização.

[As convenções de autorização](xref:security/authorization/razor-pages-authorization) de páginas de barbear não se aplicam a componentes de lâminas de barbear. Se um componente razor não routable for [incorporado em uma página,](xref:blazor/integrate-components#render-components-from-a-page-or-view)as convenções de autorização da página afetarão indiretamente o componente Razor, juntamente com o resto do conteúdo da página.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> não são suportados em componentes razor.

## <a name="authentication"></a>Autenticação

Blazorusa os mecanismos de autenticação ASP.NET Core existentes para estabelecer a identidade do usuário. O mecanismo exato depende Blazor de como o Blazor aplicativo Blazor está hospedado, WebAssembly ou Server.

### <a name="opno-locblazor-webassembly-authentication"></a>BlazorAutenticação do WebAssembly

Nos Blazor aplicativos WebAssembly, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado pelos usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

Adicione o seguinte:

* Uma referência de pacote para [Microsoft.AspNetCore.Components.Autorização](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) para o arquivo de projeto do aplicativo.
* O `Microsoft.AspNetCore.Components.Authorization` namespace para o arquivo *_Imports.razor* do aplicativo.

Para lidar com a autenticação, a `AuthenticationStateProvider` implementação de um serviço integrado ou personalizado é coberta nas seguintes seções.

Para obter mais informações sobre <xref:security/blazor/webassembly/index>a criação de aplicativos e configuração, consulte .

### <a name="opno-locblazor-server-authentication"></a>BlazorAutenticação do servidor

BlazorOs aplicativos do servidor operam através de uma SignalRconexão em tempo real criada usando . [A autenticação em aplicativos SignalRbaseados é](xref:signalr/authn-and-authz) tratada quando a conexão é estabelecida. A autenticação pode ser baseada em um cookie ou um algum outro token de portador.

Para obter mais informações sobre <xref:security/blazor/server>a criação de aplicativos e configuração, consulte .

## <a name="authenticationstateprovider-service"></a>Serviço AuthenticationStateProvider

O serviço `AuthenticationStateProvider` incorporado obtém dados do estado de `HttpContext.User`autenticação do ASP.NET Core. É assim que o estado de autenticação se integra aos mecanismos de autenticação ASP.NET Core existentes.

O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.

Normalmente, você não usa o `AuthenticationStateProvider` diretamente. Use o [componente AuthorizeView](#authorizeview-component) ou as abordagens [de Estado>de autenticação de tarefas\<](#expose-the-authentication-state-as-a-cascading-parameter) descritas mais tarde neste artigo. A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.

O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:

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
            <li>@claim.Type &ndash; @claim.Value</li>
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

Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementar um AuthenticationStateProvider personalizado

Se o aplicativo exigir um `AuthenticationStateProvider` provedor `GetAuthenticationStateAsync`personalizado, implemente e anule:

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

Em Blazor um aplicativo WebAssembly, o `CustomAuthStateProvider` `Main` serviço é registrado em *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Em Blazor um aplicativo `CustomAuthStateProvider` do Servidor, `Startup.ConfigureServices`o serviço é registrado em :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Usando `CustomAuthStateProvider` o exemplo anterior, todos os usuários são `mrfibuli`autenticados com o nome de usuário .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Expor o estado de autenticação como um parâmetro em cascata

Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:

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

Configure `Task<AuthenticationState>` o parâmetro em cascata `AuthorizeRouteView` `CascadingAuthenticationState` usando os `App` componentes do componente *(App.razor):*

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Em Blazor um aplicativo WebAssembly, adicione serviços para opções e autorização para: `Program.Main`

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Em Blazor um aplicativo do Servidor, serviços para opções e autorização já estão presentes, portanto, nenhuma ação adicional é necessária.

## <a name="authorization"></a>Autorização

Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.

O acesso geralmente é concedido ou negado com base nos seguintes casos:

* Se o usuário está autenticado (conectado).
* Se o usuário está em uma *função*.
* Se o usuário tem uma *declaração*.
* Se uma *política* é atendida.

Todos esses conceitos são iguais no MVC do ASP.NET Core ou em aplicativos Razor Pages. Para obter mais informações sobre a segurança do ASP.NET Core, confira os artigos em [Identidade e segurança do ASP.NET Core](xref:security/index).

## <a name="authorizeview-component"></a>Componente AuthorizeView

O componente `AuthorizeView` exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la. Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.

O componente expõe uma variável `context` do tipo `AuthenticationState`, que pode ser usada para acessar informações sobre o usuário conectado:

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

O `AuthorizeView` componente pode ser `NavMenu` usado no componente *(Compartilhado/NavMenu.razor)* para`<li>...</li>`exibir `NavLink`um item de lista ( ) para um , mas observe que esta abordagem só remove o item da lista da saída renderizada. Isso não impede que o usuário navegue até o componente.

O conteúdo `<Authorized>` `<NotAuthorized>` e as tags podem incluir itens arbitrários, como outros componentes interativos.

As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).

Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

### <a name="role-based-and-policy-based-authorization"></a>Autorização baseada em funções e em políticas

O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.

Para a autorização baseada em funções, use o parâmetro `Roles`:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Para obter mais informações, consulte <xref:security/authorization/roles>.

Para a autorização baseada em políticas, use o parâmetro `Policy`:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

A autorização baseada em declarações é um caso especial de autorização baseada em políticas. Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração. Para obter mais informações, consulte <xref:security/authorization/policies>.

Essas APIs podem ser Blazor usadas Blazor em aplicativos do Servidor ou do WebAssembly.

Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.

### <a name="content-displayed-during-asynchronous-authentication"></a>Conteúdo exibido durante a autenticação assíncrona

Blazorpermite que o estado de autenticação seja determinado *de forma assíncrona*. O cenário principal para Blazor essa abordagem está nos aplicativos WebAssembly que fazem uma solicitação a um ponto final externo para autenticação.

Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão. Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:

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

Essa abordagem normalmente não Blazor é aplicável aos aplicativos do Server. BlazorOs aplicativos do servidor sabem o estado de autenticação assim que o estado é estabelecido. `Authorizing`o conteúdo pode ser Blazor fornecido no `AuthorizeView` componente de um aplicativo do Servidor, mas o conteúdo nunca é exibido.

## <a name="authorize-attribute"></a>Atributo [Authorize]

O `[Authorize]` atributo pode ser usado em componentes da Navalha:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Use `[Authorize]` somente `@page` em componentes Blazor alcançados através do Roteador. A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página. Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.

O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas. Para a autorização baseada em funções, use o parâmetro `Roles`:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Para a autorização baseada em políticas, use o parâmetro `Policy`:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personalizar conteúdo não autorizado com o componente Router

O `Router` componente, em `AuthorizeRouteView` conjunto com o componente, permite que o aplicativo especifique conteúdo personalizado se:

* O conteúdo não for encontrado.
* O usuário não atender à condição `[Authorize]` aplicada ao componente. O `[Authorize]` atributo está coberto na seção [ `[Authorize]` de atributos.](#authorize-attribute)
* A autenticação assíncrona estiver em andamento.

No modelo Blazor de projeto `App` padrão do Servidor, o componente *(App.razor)* demonstra como definir conteúdo personalizado:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
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
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

O conteúdo `<NotFound>` `<NotAuthorized>`de `<Authorizing>` , e tags podem incluir itens arbitrários, como outros componentes interativos.

Se `<NotAuthorized>` o elemento não for `AuthorizeRouteView` especificado, o usará a seguinte mensagem de recuo:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notificação sobre mudanças no estado de autenticação

Se o aplicativo determinar que os dados do estado de autenticação subjacentes foram alterados (por exemplo, porque o usuário saiu `NotifyAuthenticationStateChanged` ou `AuthenticationStateProvider` outro usuário alterou suas funções), um Provedor de [Autenticação personalizado](#implement-a-custom-authenticationstateprovider) pode, opcionalmente, invocar o método na classe base. Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.

## <a name="procedural-logic"></a>Lógica de procedimento

Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário. O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.

```razor
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
> Em Blazor um componente de aplicativo `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` WebAssembly, adicione os espaços de nome e:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Esses namespaces podem ser fornecidos globalmente adicionando-os ao arquivo *_Imports.razor* do aplicativo.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorização Blazor em aplicativos WebAssembly

Nos Blazor aplicativos WebAssembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado pelos usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**

Para obter mais informações, <xref:security/blazor/webassembly/index>consulte os artigos em .

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Erros comuns:

* **A autorização requer um parâmetro em\<cascata do tipo Autenticação de tarefasEstado>. Considere usar o CascadingAuthenticationState para fornecer isso.**

* **`null`valor é recebido para`authenticationStateTask`**

É provável que o projeto não tenha Blazor sido criado usando um modelo do Servidor com autenticação ativada. Enrole `<CascadingAuthenticationState>` uma parte da árvore de ia, por exemplo, no `App` componente *(App.razor)* da seguinte forma:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Incrível Blazor: Links](https://github.com/AdrienTorris/awesome-blazor#authentication) de amostra da comunidade de autenticação
