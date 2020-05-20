---
<span data-ttu-id="50206-101">Título: ' ASP.NET Core Blazor autenticação e autorização ' autor: Descrição: ' saiba mais sobre Blazor cenários de autenticação e autorização '.</span><span class="sxs-lookup"><span data-stu-id="50206-101">title: 'ASP.NET Core Blazor authentication and authorization' author: description: 'Learn about Blazor authentication and authorization scenarios.'</span></span>
<span data-ttu-id="50206-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="50206-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="50206-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="50206-103">'Blazor'</span></span>
- <span data-ttu-id="50206-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="50206-104">'Identity'</span></span>
- <span data-ttu-id="50206-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="50206-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="50206-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="50206-106">'Razor'</span></span>
- <span data-ttu-id="50206-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="50206-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="50206-108">BlazorAutenticação e autorização do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50206-108">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="50206-109">Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50206-109">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50206-110">O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="50206-110">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="50206-111">Cenários de segurança diferem entre Blazor aplicativos de servidor e Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="50206-111">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="50206-112">Como os Blazor aplicativos de servidor são executados no servidor, as verificações de autorização são capazes de determinar:</span><span class="sxs-lookup"><span data-stu-id="50206-112">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="50206-113">As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).</span><span class="sxs-lookup"><span data-stu-id="50206-113">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="50206-114">As regras de acesso para áreas do aplicativo e componentes.</span><span class="sxs-lookup"><span data-stu-id="50206-114">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="50206-115">Os aplicativos Webassembly são executados no cliente.</span><span class="sxs-lookup"><span data-stu-id="50206-115"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="50206-116">A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas.</span><span class="sxs-lookup"><span data-stu-id="50206-116">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="50206-117">Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um Blazor aplicativo Webassembly não pode impor regras de acesso de autorização.</span><span class="sxs-lookup"><span data-stu-id="50206-117">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="50206-118">As [ Razor convenções de autorização de páginas](xref:security/authorization/razor-pages-authorization) não se aplicam a componentes roteáveis Razor .</span><span class="sxs-lookup"><span data-stu-id="50206-118">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="50206-119">Se um componente não roteável Razor for [inserido em uma página](xref:blazor/integrate-components#render-components-from-a-page-or-view), as convenções de autorização da página afetarão indiretamente o Razor componente junto com o restante do conteúdo da página.</span><span class="sxs-lookup"><span data-stu-id="50206-119">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="50206-120"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> não têm suporte em Razor componentes do.</span><span class="sxs-lookup"><span data-stu-id="50206-120"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="50206-121">Autenticação</span><span class="sxs-lookup"><span data-stu-id="50206-121">Authentication</span></span>

Blazor<span data-ttu-id="50206-122">usa os mecanismos de autenticação de ASP.NET Core existentes para estabelecer a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="50206-122"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="50206-123">O mecanismo exato depende de como o Blazor aplicativo é hospedado, Blazor Webassembly ou Blazor servidor.</span><span class="sxs-lookup"><span data-stu-id="50206-123">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="50206-124">Autenticação de Webassembly</span><span class="sxs-lookup"><span data-stu-id="50206-124"> WebAssembly authentication</span></span>

<span data-ttu-id="50206-125">Em Blazor aplicativos Webassembly, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários.</span><span class="sxs-lookup"><span data-stu-id="50206-125">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="50206-126">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="50206-126">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="50206-127">Adicione o seguinte:</span><span class="sxs-lookup"><span data-stu-id="50206-127">Add the following:</span></span>

* <span data-ttu-id="50206-128">Uma referência de pacote para [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) para o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="50206-128">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="50206-129">O `Microsoft.AspNetCore.Components.Authorization` namespace para o arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="50206-129">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="50206-130">Para lidar com a autenticação, a implementação de um serviço interno ou personalizado `AuthenticationStateProvider` é abordada nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="50206-130">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="50206-131">Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:security/blazor/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="50206-131">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="50206-132">Autenticação do servidor</span><span class="sxs-lookup"><span data-stu-id="50206-132"> Server authentication</span></span>

Blazor<span data-ttu-id="50206-133">Os aplicativos de servidor operam em uma conexão em tempo real que é criada usando o SignalR .</span><span class="sxs-lookup"><span data-stu-id="50206-133"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="50206-134">A [autenticação em SignalR aplicativos baseados no](xref:signalr/authn-and-authz) é tratada quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="50206-134">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="50206-135">A autenticação pode ser baseada em um cookie ou um algum outro token de portador.</span><span class="sxs-lookup"><span data-stu-id="50206-135">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="50206-136">Para obter mais informações sobre como criar aplicativos e configuração, consulte <xref:security/blazor/server/index> .</span><span class="sxs-lookup"><span data-stu-id="50206-136">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="50206-137">Serviço AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="50206-137">AuthenticationStateProvider service</span></span>

<span data-ttu-id="50206-138">O `AuthenticationStateProvider` serviço interno obtém dados de estado de autenticação do ASP.NET Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="50206-138">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="50206-139">É assim que o estado de autenticação se integra aos mecanismos existentes de autenticação de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50206-139">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="50206-140">O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="50206-140">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="50206-141">Normalmente, você não usa o `AuthenticationStateProvider` diretamente.</span><span class="sxs-lookup"><span data-stu-id="50206-141">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="50206-142">Use o [componente AuthorizeView](#authorizeview-component) ou a [tarefa \< authenticationstate>](#expose-the-authentication-state-as-a-cascading-parameter) abordagens descritas posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="50206-142">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="50206-143">A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.</span><span class="sxs-lookup"><span data-stu-id="50206-143">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="50206-144">O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="50206-144">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="50206-145">Se `user.Identity.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="50206-145">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="50206-146">Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="50206-146">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="50206-147">Implementar um AuthenticationStateProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="50206-147">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="50206-148">Se o aplicativo exigir um provedor personalizado, implemente `AuthenticationStateProvider` e substitua `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="50206-148">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="50206-149">Em um Blazor aplicativo Webassembly, o `CustomAuthStateProvider` serviço é registrado em `Main` *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="50206-149">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="50206-150">Em um Blazor aplicativo de servidor, o `CustomAuthStateProvider` serviço é registrado em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="50206-150">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="50206-151">Usando o `CustomAuthStateProvider` no exemplo anterior, todos os usuários são autenticados com o nome de usuário `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="50206-151">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="50206-152">Expor o estado de autenticação como um parâmetro em cascata</span><span class="sxs-lookup"><span data-stu-id="50206-152">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="50206-153">Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="50206-153">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="50206-154">Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="50206-154">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="50206-155">Configure o `Task<AuthenticationState>` parâmetro em cascata usando os `AuthorizeRouteView` componentes e `CascadingAuthenticationState` no `App` componente (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="50206-155">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="50206-156">Em um Blazor aplicativo Webassembly, adicione serviços para opções e autorização para `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="50206-156">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="50206-157">Em um Blazor aplicativo de servidor, os serviços para opções e autorização já estão presentes, portanto, nenhuma ação adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="50206-157">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="50206-158">Autorização</span><span class="sxs-lookup"><span data-stu-id="50206-158">Authorization</span></span>

<span data-ttu-id="50206-159">Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.</span><span class="sxs-lookup"><span data-stu-id="50206-159">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="50206-160">O acesso geralmente é concedido ou negado com base nos seguintes casos:</span><span class="sxs-lookup"><span data-stu-id="50206-160">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="50206-161">Se o usuário está autenticado (conectado).</span><span class="sxs-lookup"><span data-stu-id="50206-161">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="50206-162">Se o usuário está em uma *função*.</span><span class="sxs-lookup"><span data-stu-id="50206-162">A user is in a *role*.</span></span>
* <span data-ttu-id="50206-163">Se o usuário tem uma *declaração*.</span><span class="sxs-lookup"><span data-stu-id="50206-163">A user has a *claim*.</span></span>
* <span data-ttu-id="50206-164">Se uma *política* é atendida.</span><span class="sxs-lookup"><span data-stu-id="50206-164">A *policy* is satisfied.</span></span>

<span data-ttu-id="50206-165">Cada um desses conceitos é o mesmo de um aplicativo ASP.NET Core MVC ou de Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="50206-165">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="50206-166">Para obter mais informações sobre ASP.NET Core segurança, consulte os artigos em [ASP.NET Core segurança Identity e ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="50206-166">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="50206-167">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="50206-167">AuthorizeView component</span></span>

<span data-ttu-id="50206-168">O componente `AuthorizeView` exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la.</span><span class="sxs-lookup"><span data-stu-id="50206-168">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="50206-169">Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.</span><span class="sxs-lookup"><span data-stu-id="50206-169">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="50206-170">O componente expõe uma variável `context` do tipo `AuthenticationState`, que pode ser usada para acessar informações sobre o usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="50206-170">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="50206-171">Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:</span><span class="sxs-lookup"><span data-stu-id="50206-171">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="50206-172">O `AuthorizeView` componente pode ser usado no `NavMenu` componente (*Shared/NavMenu. Razor*) para exibir um item de lista ( `<li>...</li>` ) para um `NavLink` , mas observe que essa abordagem apenas remove o item de lista da saída renderizada.</span><span class="sxs-lookup"><span data-stu-id="50206-172">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="50206-173">Ele não impede que o usuário navegue até o componente.</span><span class="sxs-lookup"><span data-stu-id="50206-173">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="50206-174">O conteúdo de `<Authorized>` `<NotAuthorized>` marcas e pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="50206-174">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="50206-175">As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).</span><span class="sxs-lookup"><span data-stu-id="50206-175">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="50206-176">Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:</span><span class="sxs-lookup"><span data-stu-id="50206-176">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="50206-177">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="50206-177">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="50206-178">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="50206-178">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="50206-179">Autorização baseada em funções e em políticas</span><span class="sxs-lookup"><span data-stu-id="50206-179">Role-based and policy-based authorization</span></span>

<span data-ttu-id="50206-180">O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.</span><span class="sxs-lookup"><span data-stu-id="50206-180">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="50206-181">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="50206-181">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="50206-182">Para obter mais informações, consulte <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="50206-182">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="50206-183">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="50206-183">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="50206-184">A autorização baseada em declarações é um caso especial de autorização baseada em políticas.</span><span class="sxs-lookup"><span data-stu-id="50206-184">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="50206-185">Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração.</span><span class="sxs-lookup"><span data-stu-id="50206-185">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="50206-186">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="50206-186">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="50206-187">Essas APIs podem ser usadas em Blazor aplicativos de servidor ou Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="50206-187">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="50206-188">Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.</span><span class="sxs-lookup"><span data-stu-id="50206-188">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="50206-189">Conteúdo exibido durante a autenticação assíncrona</span><span class="sxs-lookup"><span data-stu-id="50206-189">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="50206-190">permite que o estado de autenticação seja determinado de *forma assíncrona*.</span><span class="sxs-lookup"><span data-stu-id="50206-190"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="50206-191">O cenário principal para essa abordagem é em Blazor aplicativos Webassembly que fazem uma solicitação para um ponto de extremidade externo para autenticação.</span><span class="sxs-lookup"><span data-stu-id="50206-191">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="50206-192">Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão.</span><span class="sxs-lookup"><span data-stu-id="50206-192">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="50206-193">Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="50206-193">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="50206-194">Essa abordagem não é normalmente aplicável a Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="50206-194">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="50206-195">Os aplicativos de servidor conhecem o estado de autenticação assim que o estado é estabelecido.</span><span class="sxs-lookup"><span data-stu-id="50206-195"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="50206-196">`Authorizing`o conteúdo pode ser fornecido no Blazor componente de um aplicativo de servidor `AuthorizeView` , mas o conteúdo nunca é exibido.</span><span class="sxs-lookup"><span data-stu-id="50206-196">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="50206-197">Atributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="50206-197">[Authorize] attribute</span></span>

<span data-ttu-id="50206-198">O `[Authorize]` atributo pode ser usado em Razor componentes:</span><span class="sxs-lookup"><span data-stu-id="50206-198">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="50206-199">Somente `[Authorize]` o uso em `@page` componentes foi atingido por meio do Blazor roteador.</span><span class="sxs-lookup"><span data-stu-id="50206-199">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="50206-200">A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página.</span><span class="sxs-lookup"><span data-stu-id="50206-200">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="50206-201">Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="50206-201">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="50206-202">O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas.</span><span class="sxs-lookup"><span data-stu-id="50206-202">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="50206-203">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="50206-203">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="50206-204">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="50206-204">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="50206-205">Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:</span><span class="sxs-lookup"><span data-stu-id="50206-205">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="50206-206">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="50206-206">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="50206-207">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="50206-207">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="50206-208">Personalizar conteúdo não autorizado com o componente Router</span><span class="sxs-lookup"><span data-stu-id="50206-208">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="50206-209">O `Router` componente, em conjunto com o `AuthorizeRouteView` componente, permite que o aplicativo especifique o conteúdo personalizado se:</span><span class="sxs-lookup"><span data-stu-id="50206-209">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="50206-210">O conteúdo não for encontrado.</span><span class="sxs-lookup"><span data-stu-id="50206-210">Content isn't found.</span></span>
* <span data-ttu-id="50206-211">O usuário não atender à condição `[Authorize]` aplicada ao componente.</span><span class="sxs-lookup"><span data-stu-id="50206-211">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="50206-212">O `[Authorize]` atributo é abordado na seção [ `[Authorize]` atributo](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="50206-212">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="50206-213">A autenticação assíncrona estiver em andamento.</span><span class="sxs-lookup"><span data-stu-id="50206-213">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="50206-214">No modelo de Blazor projeto de servidor padrão, o `App` componente (*app. Razor*) demonstra como definir conteúdo personalizado:</span><span class="sxs-lookup"><span data-stu-id="50206-214">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="50206-215">O conteúdo das `<NotFound>` `<NotAuthorized>` marcas, e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="50206-215">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="50206-216">Se o `<NotAuthorized>` elemento não for especificado, o `AuthorizeRouteView` usará a seguinte mensagem de fallback:</span><span class="sxs-lookup"><span data-stu-id="50206-216">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="50206-217">Notificação sobre mudanças no estado de autenticação</span><span class="sxs-lookup"><span data-stu-id="50206-217">Notification about authentication state changes</span></span>

<span data-ttu-id="50206-218">Se o aplicativo determinar que os dados de estado de autenticação subjacentes foram alterados (por exemplo, porque o usuário se desconectou ou outro usuário alterou suas funções), um [AuthenticationStateProvider personalizado](#implement-a-custom-authenticationstateprovider) poderá, opcionalmente, invocar o método `NotifyAuthenticationStateChanged` na `AuthenticationStateProvider` classe base.</span><span class="sxs-lookup"><span data-stu-id="50206-218">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="50206-219">Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.</span><span class="sxs-lookup"><span data-stu-id="50206-219">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="50206-220">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="50206-220">Procedural logic</span></span>

<span data-ttu-id="50206-221">Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário.</span><span class="sxs-lookup"><span data-stu-id="50206-221">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="50206-222">O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.</span><span class="sxs-lookup"><span data-stu-id="50206-222">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="50206-223">Em um Blazor componente de aplicativo Webassembly, adicione `Microsoft.AspNetCore.Authorization` os `Microsoft.AspNetCore.Components.Authorization` namespaces e:</span><span class="sxs-lookup"><span data-stu-id="50206-223">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="50206-224">Esses namespaces podem ser fornecidos globalmente adicionando-os ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="50206-224">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="50206-225">Autorização em Blazor aplicativos Webassembly</span><span class="sxs-lookup"><span data-stu-id="50206-225">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="50206-226">Em Blazor aplicativos Webassembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários.</span><span class="sxs-lookup"><span data-stu-id="50206-226">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="50206-227">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="50206-227">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="50206-228">**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**</span><span class="sxs-lookup"><span data-stu-id="50206-228">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="50206-229">Para obter mais informações, consulte os artigos em <xref:security/blazor/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="50206-229">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="50206-230">Solucionar problemas de erros</span><span class="sxs-lookup"><span data-stu-id="50206-230">Troubleshoot errors</span></span>

<span data-ttu-id="50206-231">Erros comuns:</span><span class="sxs-lookup"><span data-stu-id="50206-231">Common errors:</span></span>

* <span data-ttu-id="50206-232">**A autorização requer um parâmetro em cascata do tipo Task \< authenticationstate>. Considere o uso de CascadingAuthenticationState para fornecer isso.**</span><span class="sxs-lookup"><span data-stu-id="50206-232">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="50206-233">**`null`o valor é recebido para`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="50206-233">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="50206-234">É provável que o projeto não tenha sido criado usando um Blazor modelo de servidor com autenticação habilitada.</span><span class="sxs-lookup"><span data-stu-id="50206-234">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="50206-235">Envolva uma `<CascadingAuthenticationState>` parte da árvore de interface do usuário, por exemplo, no `App` componente (*app. Razor*) da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="50206-235">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="50206-236">O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="50206-236">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50206-237">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="50206-237">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="50206-238">[Incrível Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) links de exemplo da comunidade de autenticação</span><span class="sxs-lookup"><span data-stu-id="50206-238">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
