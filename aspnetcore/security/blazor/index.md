---
Título: ' ASP.NET Core Blazor autenticação e autorização ' autor: Descrição: ' saiba mais sobre Blazor cenários de autenticação e autorização '.
monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRuid ' ': 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>BlazorAutenticação e autorização do ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)

O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em Blazor aplicativos.

Cenários de segurança diferem entre Blazor aplicativos de servidor e Blazor Webassembly. Como os Blazor aplicativos de servidor são executados no servidor, as verificações de autorização são capazes de determinar:

* As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).
* As regras de acesso para áreas do aplicativo e componentes.

BlazorOs aplicativos Webassembly são executados no cliente. A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas. Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um Blazor aplicativo Webassembly não pode impor regras de acesso de autorização.

As [ Razor convenções de autorização de páginas](xref:security/authorization/razor-pages-authorization) não se aplicam a componentes roteáveis Razor . Se um componente não roteável Razor for [inserido em uma página](xref:blazor/integrate-components#render-components-from-a-page-or-view), as convenções de autorização da página afetarão indiretamente o Razor componente junto com o restante do conteúdo da página.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> não têm suporte em Razor componentes do.

## <a name="authentication"></a>Autenticação

Blazorusa os mecanismos de autenticação de ASP.NET Core existentes para estabelecer a identidade do usuário. O mecanismo exato depende de como o Blazor aplicativo é hospedado, Blazor Webassembly ou Blazor servidor.

### <a name="blazor-webassembly-authentication"></a>BlazorAutenticação de Webassembly

Em Blazor aplicativos Webassembly, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

Adicione o seguinte:

* Uma referência de pacote para [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) para o arquivo de projeto do aplicativo.
* O `Microsoft.AspNetCore.Components.Authorization` namespace para o arquivo *_Imports. Razor* do aplicativo.

Para lidar com a autenticação, a implementação de um serviço interno ou personalizado `AuthenticationStateProvider` é abordada nas seções a seguir.

Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:security/blazor/webassembly/index> .

### <a name="blazor-server-authentication"></a>BlazorAutenticação do servidor

BlazorOs aplicativos de servidor operam em uma conexão em tempo real que é criada usando o SignalR . A [autenticação em SignalR aplicativos baseados no](xref:signalr/authn-and-authz) é tratada quando a conexão é estabelecida. A autenticação pode ser baseada em um cookie ou um algum outro token de portador.

Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:security/blazor/server/index> .

## <a name="authenticationstateprovider-service"></a>Serviço AuthenticationStateProvider

O `AuthenticationStateProvider` serviço interno obtém dados de estado de autenticação do ASP.NET Core `HttpContext.User` . É assim que o estado de autenticação se integra aos mecanismos existentes de autenticação de ASP.NET Core.

O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.

Normalmente, você não usa o `AuthenticationStateProvider` diretamente. Use o [componente AuthorizeView](#authorizeview-component) ou a [tarefa \< authenticationstate>](#expose-the-authentication-state-as-a-cascading-parameter) abordagens descritas posteriormente neste artigo. A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.

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

Se o aplicativo exigir um provedor personalizado, implemente `AuthenticationStateProvider` e substitua `GetAuthenticationStateAsync` :

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

Em um Blazor aplicativo Webassembly, o `CustomAuthStateProvider` serviço é registrado em `Main` *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Em um Blazor aplicativo de servidor, o `CustomAuthStateProvider` serviço é registrado em `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Usando o `CustomAuthStateProvider` no exemplo anterior, todos os usuários são autenticados com o nome de usuário `mrfibuli` .

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

Configure o `Task<AuthenticationState>` parâmetro em cascata usando os `AuthorizeRouteView` componentes e `CascadingAuthenticationState` no `App` componente (*app. Razor*):

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

Em um Blazor aplicativo Webassembly, adicione serviços para opções e autorização para `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Em um Blazor aplicativo de servidor, os serviços para opções e autorização já estão presentes, portanto, nenhuma ação adicional é necessária.

## <a name="authorization"></a>Autorização

Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.

O acesso geralmente é concedido ou negado com base nos seguintes casos:

* Se o usuário está autenticado (conectado).
* Se o usuário está em uma *função*.
* Se o usuário tem uma *declaração*.
* Se uma *política* é atendida.

Cada um desses conceitos é o mesmo de um aplicativo ASP.NET Core MVC ou de Razor páginas. Para obter mais informações sobre ASP.NET Core segurança, consulte os artigos em [ASP.NET Core segurança Identity e ](xref:security/index).

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

O `AuthorizeView` componente pode ser usado no `NavMenu` componente (*Shared/NavMenu. Razor*) para exibir um item de lista ( `<li>...</li>` ) para um `NavLink` , mas observe que essa abordagem apenas remove o item de lista da saída renderizada. Ele não impede que o usuário navegue até o componente.

O conteúdo de `<Authorized>` `<NotAuthorized>` marcas e pode incluir itens arbitrários, como outros componentes interativos.

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

Essas APIs podem ser usadas em Blazor aplicativos de servidor ou Blazor Webassembly.

Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.

### <a name="content-displayed-during-asynchronous-authentication"></a>Conteúdo exibido durante a autenticação assíncrona

Blazorpermite que o estado de autenticação seja determinado de *forma assíncrona*. O cenário principal para essa abordagem é em Blazor aplicativos Webassembly que fazem uma solicitação para um ponto de extremidade externo para autenticação.

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

Essa abordagem não é normalmente aplicável a Blazor aplicativos de servidor. BlazorOs aplicativos de servidor conhecem o estado de autenticação assim que o estado é estabelecido. `Authorizing`o conteúdo pode ser fornecido no Blazor componente de um aplicativo de servidor `AuthorizeView` , mas o conteúdo nunca é exibido.

## <a name="authorize-attribute"></a>Atributo [Authorize]

O `[Authorize]` atributo pode ser usado em Razor componentes:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Somente `[Authorize]` o uso em `@page` componentes foi atingido por meio do Blazor roteador. A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página. Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.

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

O `Router` componente, em conjunto com o `AuthorizeRouteView` componente, permite que o aplicativo especifique o conteúdo personalizado se:

* O conteúdo não for encontrado.
* O usuário não atender à condição `[Authorize]` aplicada ao componente. O `[Authorize]` atributo é abordado na seção [ `[Authorize]` atributo](#authorize-attribute) .
* A autenticação assíncrona estiver em andamento.

No modelo de Blazor projeto de servidor padrão, o `App` componente (*app. Razor*) demonstra como definir conteúdo personalizado:

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

O conteúdo das `<NotFound>` `<NotAuthorized>` marcas, e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.

Se o `<NotAuthorized>` elemento não for especificado, o `AuthorizeRouteView` usará a seguinte mensagem de fallback:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notificação sobre mudanças no estado de autenticação

Se o aplicativo determinar que os dados de estado de autenticação subjacentes foram alterados (por exemplo, porque o usuário se desconectou ou outro usuário alterou suas funções), um [AuthenticationStateProvider personalizado](#implement-a-custom-authenticationstateprovider) poderá, opcionalmente, invocar o método `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` classe base. Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.

## <a name="procedural-logic"></a>Lógica de procedimento

Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário. O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.

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
> Em um Blazor componente de aplicativo Webassembly, adicione `Microsoft.AspNetCore.Authorization` os `Microsoft.AspNetCore.Components.Authorization` namespaces e:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Esses namespaces podem ser fornecidos globalmente adicionando-os ao arquivo *_Imports. Razor* do aplicativo.

## <a name="authorization-in-blazor-webassembly-apps"></a>Autorização em Blazor aplicativos Webassembly

Em Blazor aplicativos Webassembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**

Para obter mais informações, consulte os artigos em <xref:security/blazor/webassembly/index> .

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Erros comuns:

* **A autorização requer um parâmetro em cascata do tipo Task \< authenticationstate>. Considere o uso de CascadingAuthenticationState para fornecer isso.**

* **`null`o valor é recebido para`authenticationStateTask`**

É provável que o projeto não tenha sido criado usando um Blazor modelo de servidor com autenticação habilitada. Envolva uma `<CascadingAuthenticationState>` parte da árvore de interface do usuário, por exemplo, no `App` componente (*app. Razor*) da seguinte maneira:

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
* <xref:security/authentication/windowsauth>
* [Incrível Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) links de exemplo da comunidade de autenticação
