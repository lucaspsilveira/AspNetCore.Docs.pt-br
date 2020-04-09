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
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="10627-103">autenticação Blazor e autorização do Núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="10627-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="10627-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10627-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="10627-105">Para a orientação deste artigo Blazor que se aplica Blazor ao WebAssembly, é necessário o modelo do ASP.NET Core WebAssembly versão 3.2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="10627-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="10627-106">Se você não estiver usando a versão 16.6 Preview Blazor 2 ou posterior do <xref:blazor/get-started>Visual Studio, obtenha o modelo mais recente do WebAssembly seguindo a orientação em .</span><span class="sxs-lookup"><span data-stu-id="10627-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="10627-107">ASP.NET Core suporta a configuração Blazor e o gerenciamento de segurança em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="10627-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="10627-108">Os cenários Blazor de Blazor segurança diferem entre aplicativos Server e WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="10627-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="10627-109">Como Blazor os aplicativos do Servidor são executados no servidor, as verificações de autorização são capazes de determinar:</span><span class="sxs-lookup"><span data-stu-id="10627-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="10627-110">As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).</span><span class="sxs-lookup"><span data-stu-id="10627-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="10627-111">As regras de acesso para áreas do aplicativo e componentes.</span><span class="sxs-lookup"><span data-stu-id="10627-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="10627-112">Os aplicativos WebAssembly são executados no cliente.</span><span class="sxs-lookup"><span data-stu-id="10627-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="10627-113">A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas.</span><span class="sxs-lookup"><span data-stu-id="10627-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="10627-114">Uma vez que as verificações do lado do Blazor cliente podem ser modificadas ou ignoradas por um usuário, um aplicativo webassembly não pode impor regras de acesso à autorização.</span><span class="sxs-lookup"><span data-stu-id="10627-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="10627-115">[As convenções de autorização](xref:security/authorization/razor-pages-authorization) de páginas de barbear não se aplicam a componentes de lâminas de barbear.</span><span class="sxs-lookup"><span data-stu-id="10627-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="10627-116">Se um componente razor não routable for [incorporado em uma página,](xref:blazor/integrate-components#render-components-from-a-page-or-view)as convenções de autorização da página afetarão indiretamente o componente Razor, juntamente com o resto do conteúdo da página.</span><span class="sxs-lookup"><span data-stu-id="10627-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="10627-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> não são suportados em componentes razor.</span><span class="sxs-lookup"><span data-stu-id="10627-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="10627-118">Autenticação</span><span class="sxs-lookup"><span data-stu-id="10627-118">Authentication</span></span>

Blazor<span data-ttu-id="10627-119">usa os mecanismos de autenticação ASP.NET Core existentes para estabelecer a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="10627-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="10627-120">O mecanismo exato depende Blazor de como o Blazor aplicativo Blazor está hospedado, WebAssembly ou Server.</span><span class="sxs-lookup"><span data-stu-id="10627-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="10627-121">Autenticação do WebAssembly</span><span class="sxs-lookup"><span data-stu-id="10627-121"> WebAssembly authentication</span></span>

<span data-ttu-id="10627-122">Nos Blazor aplicativos WebAssembly, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado pelos usuários.</span><span class="sxs-lookup"><span data-stu-id="10627-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="10627-123">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="10627-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="10627-124">Adicione o seguinte:</span><span class="sxs-lookup"><span data-stu-id="10627-124">Add the following:</span></span>

* <span data-ttu-id="10627-125">Uma referência de pacote para [Microsoft.AspNetCore.Components.Autorização](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) para o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10627-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="10627-126">O `Microsoft.AspNetCore.Components.Authorization` namespace para o arquivo *_Imports.razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10627-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="10627-127">Para lidar com a autenticação, a `AuthenticationStateProvider` implementação de um serviço integrado ou personalizado é coberta nas seguintes seções.</span><span class="sxs-lookup"><span data-stu-id="10627-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="10627-128">Para obter mais informações sobre <xref:security/blazor/webassembly/index>a criação de aplicativos e configuração, consulte .</span><span class="sxs-lookup"><span data-stu-id="10627-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="10627-129">Autenticação do servidor</span><span class="sxs-lookup"><span data-stu-id="10627-129"> Server authentication</span></span>

Blazor<span data-ttu-id="10627-130">Os aplicativos do servidor operam através de uma SignalRconexão em tempo real criada usando .</span><span class="sxs-lookup"><span data-stu-id="10627-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="10627-131">[A autenticação em aplicativos SignalRbaseados é](xref:signalr/authn-and-authz) tratada quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="10627-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="10627-132">A autenticação pode ser baseada em um cookie ou um algum outro token de portador.</span><span class="sxs-lookup"><span data-stu-id="10627-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="10627-133">Para obter mais informações sobre <xref:security/blazor/server>a criação de aplicativos e configuração, consulte .</span><span class="sxs-lookup"><span data-stu-id="10627-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="10627-134">Serviço AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="10627-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="10627-135">O serviço `AuthenticationStateProvider` incorporado obtém dados do estado de `HttpContext.User`autenticação do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10627-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="10627-136">É assim que o estado de autenticação se integra aos mecanismos de autenticação ASP.NET Core existentes.</span><span class="sxs-lookup"><span data-stu-id="10627-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="10627-137">O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="10627-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="10627-138">Normalmente, você não usa o `AuthenticationStateProvider` diretamente.</span><span class="sxs-lookup"><span data-stu-id="10627-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="10627-139">Use o [componente AuthorizeView](#authorizeview-component) ou as abordagens [de Estado>de autenticação de tarefas\<](#expose-the-authentication-state-as-a-cascading-parameter) descritas mais tarde neste artigo.</span><span class="sxs-lookup"><span data-stu-id="10627-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="10627-140">A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.</span><span class="sxs-lookup"><span data-stu-id="10627-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="10627-141">O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="10627-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="10627-142">Se `user.Identity.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="10627-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="10627-143">Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="10627-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="10627-144">Implementar um AuthenticationStateProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="10627-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="10627-145">Se o aplicativo exigir um `AuthenticationStateProvider` provedor `GetAuthenticationStateAsync`personalizado, implemente e anule:</span><span class="sxs-lookup"><span data-stu-id="10627-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="10627-146">Em Blazor um aplicativo WebAssembly, o `CustomAuthStateProvider` `Main` serviço é registrado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="10627-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="10627-147">Em Blazor um aplicativo `CustomAuthStateProvider` do Servidor, `Startup.ConfigureServices`o serviço é registrado em :</span><span class="sxs-lookup"><span data-stu-id="10627-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="10627-148">Usando `CustomAuthStateProvider` o exemplo anterior, todos os usuários são `mrfibuli`autenticados com o nome de usuário .</span><span class="sxs-lookup"><span data-stu-id="10627-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="10627-149">Expor o estado de autenticação como um parâmetro em cascata</span><span class="sxs-lookup"><span data-stu-id="10627-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="10627-150">Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="10627-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="10627-151">Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="10627-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="10627-152">Configure `Task<AuthenticationState>` o parâmetro em cascata `AuthorizeRouteView` `CascadingAuthenticationState` usando os `App` componentes do componente *(App.razor):*</span><span class="sxs-lookup"><span data-stu-id="10627-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="10627-153">Em Blazor um aplicativo WebAssembly, adicione serviços para opções e autorização para: `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="10627-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="10627-154">Em Blazor um aplicativo do Servidor, serviços para opções e autorização já estão presentes, portanto, nenhuma ação adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="10627-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="10627-155">Autorização</span><span class="sxs-lookup"><span data-stu-id="10627-155">Authorization</span></span>

<span data-ttu-id="10627-156">Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.</span><span class="sxs-lookup"><span data-stu-id="10627-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="10627-157">O acesso geralmente é concedido ou negado com base nos seguintes casos:</span><span class="sxs-lookup"><span data-stu-id="10627-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="10627-158">Se o usuário está autenticado (conectado).</span><span class="sxs-lookup"><span data-stu-id="10627-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="10627-159">Se o usuário está em uma *função*.</span><span class="sxs-lookup"><span data-stu-id="10627-159">A user is in a *role*.</span></span>
* <span data-ttu-id="10627-160">Se o usuário tem uma *declaração*.</span><span class="sxs-lookup"><span data-stu-id="10627-160">A user has a *claim*.</span></span>
* <span data-ttu-id="10627-161">Se uma *política* é atendida.</span><span class="sxs-lookup"><span data-stu-id="10627-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="10627-162">Todos esses conceitos são iguais no MVC do ASP.NET Core ou em aplicativos Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="10627-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="10627-163">Para obter mais informações sobre a segurança do ASP.NET Core, confira os artigos em [Identidade e segurança do ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="10627-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="10627-164">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="10627-164">AuthorizeView component</span></span>

<span data-ttu-id="10627-165">O componente `AuthorizeView` exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la.</span><span class="sxs-lookup"><span data-stu-id="10627-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="10627-166">Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.</span><span class="sxs-lookup"><span data-stu-id="10627-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="10627-167">O componente expõe uma variável `context` do tipo `AuthenticationState`, que pode ser usada para acessar informações sobre o usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="10627-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="10627-168">Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:</span><span class="sxs-lookup"><span data-stu-id="10627-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="10627-169">O `AuthorizeView` componente pode ser `NavMenu` usado no componente *(Compartilhado/NavMenu.razor)* para`<li>...</li>`exibir `NavLink`um item de lista ( ) para um , mas observe que esta abordagem só remove o item da lista da saída renderizada.</span><span class="sxs-lookup"><span data-stu-id="10627-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="10627-170">Isso não impede que o usuário navegue até o componente.</span><span class="sxs-lookup"><span data-stu-id="10627-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="10627-171">O conteúdo `<Authorized>` `<NotAuthorized>` e as tags podem incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="10627-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="10627-172">As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).</span><span class="sxs-lookup"><span data-stu-id="10627-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="10627-173">Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:</span><span class="sxs-lookup"><span data-stu-id="10627-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="10627-174">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="10627-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="10627-175">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="10627-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="10627-176">Autorização baseada em funções e em políticas</span><span class="sxs-lookup"><span data-stu-id="10627-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="10627-177">O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.</span><span class="sxs-lookup"><span data-stu-id="10627-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="10627-178">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="10627-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="10627-179">Para obter mais informações, consulte <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="10627-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="10627-180">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="10627-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="10627-181">A autorização baseada em declarações é um caso especial de autorização baseada em políticas.</span><span class="sxs-lookup"><span data-stu-id="10627-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="10627-182">Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração.</span><span class="sxs-lookup"><span data-stu-id="10627-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="10627-183">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="10627-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="10627-184">Essas APIs podem ser Blazor usadas Blazor em aplicativos do Servidor ou do WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="10627-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="10627-185">Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.</span><span class="sxs-lookup"><span data-stu-id="10627-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="10627-186">Conteúdo exibido durante a autenticação assíncrona</span><span class="sxs-lookup"><span data-stu-id="10627-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="10627-187">permite que o estado de autenticação seja determinado *de forma assíncrona*.</span><span class="sxs-lookup"><span data-stu-id="10627-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="10627-188">O cenário principal para Blazor essa abordagem está nos aplicativos WebAssembly que fazem uma solicitação a um ponto final externo para autenticação.</span><span class="sxs-lookup"><span data-stu-id="10627-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="10627-189">Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão.</span><span class="sxs-lookup"><span data-stu-id="10627-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="10627-190">Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="10627-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="10627-191">Essa abordagem normalmente não Blazor é aplicável aos aplicativos do Server.</span><span class="sxs-lookup"><span data-stu-id="10627-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="10627-192">Os aplicativos do servidor sabem o estado de autenticação assim que o estado é estabelecido.</span><span class="sxs-lookup"><span data-stu-id="10627-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="10627-193">`Authorizing`o conteúdo pode ser Blazor fornecido no `AuthorizeView` componente de um aplicativo do Servidor, mas o conteúdo nunca é exibido.</span><span class="sxs-lookup"><span data-stu-id="10627-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="10627-194">Atributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="10627-194">[Authorize] attribute</span></span>

<span data-ttu-id="10627-195">O `[Authorize]` atributo pode ser usado em componentes da Navalha:</span><span class="sxs-lookup"><span data-stu-id="10627-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="10627-196">Use `[Authorize]` somente `@page` em componentes Blazor alcançados através do Roteador.</span><span class="sxs-lookup"><span data-stu-id="10627-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="10627-197">A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página.</span><span class="sxs-lookup"><span data-stu-id="10627-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="10627-198">Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="10627-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="10627-199">O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas.</span><span class="sxs-lookup"><span data-stu-id="10627-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="10627-200">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="10627-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="10627-201">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="10627-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="10627-202">Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:</span><span class="sxs-lookup"><span data-stu-id="10627-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="10627-203">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="10627-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="10627-204">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="10627-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="10627-205">Personalizar conteúdo não autorizado com o componente Router</span><span class="sxs-lookup"><span data-stu-id="10627-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="10627-206">O `Router` componente, em `AuthorizeRouteView` conjunto com o componente, permite que o aplicativo especifique conteúdo personalizado se:</span><span class="sxs-lookup"><span data-stu-id="10627-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="10627-207">O conteúdo não for encontrado.</span><span class="sxs-lookup"><span data-stu-id="10627-207">Content isn't found.</span></span>
* <span data-ttu-id="10627-208">O usuário não atender à condição `[Authorize]` aplicada ao componente.</span><span class="sxs-lookup"><span data-stu-id="10627-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="10627-209">O `[Authorize]` atributo está coberto na seção [ `[Authorize]` de atributos.](#authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="10627-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="10627-210">A autenticação assíncrona estiver em andamento.</span><span class="sxs-lookup"><span data-stu-id="10627-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="10627-211">No modelo Blazor de projeto `App` padrão do Servidor, o componente *(App.razor)* demonstra como definir conteúdo personalizado:</span><span class="sxs-lookup"><span data-stu-id="10627-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="10627-212">O conteúdo `<NotFound>` `<NotAuthorized>`de `<Authorizing>` , e tags podem incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="10627-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="10627-213">Se `<NotAuthorized>` o elemento não for `AuthorizeRouteView` especificado, o usará a seguinte mensagem de recuo:</span><span class="sxs-lookup"><span data-stu-id="10627-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="10627-214">Notificação sobre mudanças no estado de autenticação</span><span class="sxs-lookup"><span data-stu-id="10627-214">Notification about authentication state changes</span></span>

<span data-ttu-id="10627-215">Se o aplicativo determinar que os dados do estado de autenticação subjacentes foram alterados (por exemplo, porque o usuário saiu `NotifyAuthenticationStateChanged` ou `AuthenticationStateProvider` outro usuário alterou suas funções), um Provedor de [Autenticação personalizado](#implement-a-custom-authenticationstateprovider) pode, opcionalmente, invocar o método na classe base.</span><span class="sxs-lookup"><span data-stu-id="10627-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="10627-216">Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.</span><span class="sxs-lookup"><span data-stu-id="10627-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="10627-217">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="10627-217">Procedural logic</span></span>

<span data-ttu-id="10627-218">Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário.</span><span class="sxs-lookup"><span data-stu-id="10627-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="10627-219">O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.</span><span class="sxs-lookup"><span data-stu-id="10627-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="10627-220">Em Blazor um componente de aplicativo `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` WebAssembly, adicione os espaços de nome e:</span><span class="sxs-lookup"><span data-stu-id="10627-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="10627-221">Esses namespaces podem ser fornecidos globalmente adicionando-os ao arquivo *_Imports.razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10627-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="10627-222">Autorização Blazor em aplicativos WebAssembly</span><span class="sxs-lookup"><span data-stu-id="10627-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="10627-223">Nos Blazor aplicativos WebAssembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado pelos usuários.</span><span class="sxs-lookup"><span data-stu-id="10627-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="10627-224">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="10627-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="10627-225">**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**</span><span class="sxs-lookup"><span data-stu-id="10627-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="10627-226">Para obter mais informações, <xref:security/blazor/webassembly/index>consulte os artigos em .</span><span class="sxs-lookup"><span data-stu-id="10627-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="10627-227">Solucionar problemas de erros</span><span class="sxs-lookup"><span data-stu-id="10627-227">Troubleshoot errors</span></span>

<span data-ttu-id="10627-228">Erros comuns:</span><span class="sxs-lookup"><span data-stu-id="10627-228">Common errors:</span></span>

* <span data-ttu-id="10627-229">**A autorização requer um parâmetro em\<cascata do tipo Autenticação de tarefasEstado>. Considere usar o CascadingAuthenticationState para fornecer isso.**</span><span class="sxs-lookup"><span data-stu-id="10627-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="10627-230">**`null`valor é recebido para`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="10627-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="10627-231">É provável que o projeto não tenha Blazor sido criado usando um modelo do Servidor com autenticação ativada.</span><span class="sxs-lookup"><span data-stu-id="10627-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="10627-232">Enrole `<CascadingAuthenticationState>` uma parte da árvore de ia, por exemplo, no `App` componente *(App.razor)* da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="10627-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="10627-233">O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="10627-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10627-234">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="10627-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="10627-235">[Incrível Blazor: Links](https://github.com/AdrienTorris/awesome-blazor#authentication) de amostra da comunidade de autenticação</span><span class="sxs-lookup"><span data-stu-id="10627-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
