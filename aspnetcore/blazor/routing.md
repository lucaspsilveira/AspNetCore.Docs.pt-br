---
<span data-ttu-id="d0390-101">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-102">'Blazor'</span></span>
- <span data-ttu-id="d0390-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-103">'Identity'</span></span>
- <span data-ttu-id="d0390-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-105">'Razor'</span></span>
- <span data-ttu-id="d0390-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="d0390-107">Roteamento de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d0390-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d0390-108">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0390-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d0390-109">Saiba como rotear solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="d0390-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d0390-110">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0390-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="d0390-111">O servidor é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d0390-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d0390-112">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d0390-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d0390-113">A configuração mais típica é rotear todas as solicitações para uma Razor página, que atua como o host da parte do lado do servidor do Blazor aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="d0390-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d0390-114">Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0390-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="d0390-115">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="d0390-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d0390-116">A rota de fallback é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="d0390-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="d0390-117">Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo do Blazor servidor.</span><span class="sxs-lookup"><span data-stu-id="d0390-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d0390-118">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="d0390-118">Route templates</span></span>

<span data-ttu-id="d0390-119">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="d0390-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="d0390-120">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="d0390-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="d0390-121">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a classe gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d0390-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="d0390-122">Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="d0390-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="d0390-123">Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="d0390-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="d0390-124">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="d0390-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="d0390-125">Opcionalmente, você pode especificar um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="d0390-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="d0390-126">Os Blazor modelos padrão especificam o `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="d0390-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="d0390-127">*MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="d0390-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="d0390-128">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d0390-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d0390-129">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="d0390-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d0390-130">O componente a seguir responde a solicitações para o `/BlazorRoute` e o `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="d0390-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="d0390-131">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu arquivo *wwwroot/index.html* ( Blazor Webassembly) ou *páginas/_Host arquivo. cshtml* ( Blazor servidor) com o caminho base do aplicativo especificado no `href` atributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="d0390-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d0390-132">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d0390-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d0390-133">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="d0390-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d0390-134">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="d0390-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d0390-135">No arquivo *app. Razor* , defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parâmetro de modelo do <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:</span><span class="sxs-lookup"><span data-stu-id="d0390-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="d0390-136">O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="d0390-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="d0390-137">Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d0390-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d0390-138">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="d0390-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d0390-139">Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="d0390-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="d0390-140">Os assemblies especificados são considerados além do `AppAssembly` assembly especificado.</span><span class="sxs-lookup"><span data-stu-id="d0390-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="d0390-141">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="d0390-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="d0390-142">O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` :</span><span class="sxs-lookup"><span data-stu-id="d0390-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="d0390-143">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="d0390-143">Route parameters</span></span>

<span data-ttu-id="d0390-144">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="d0390-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="d0390-145">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="d0390-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="d0390-146">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="d0390-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d0390-147">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d0390-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d0390-148">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d0390-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d0390-149">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="d0390-149">Route constraints</span></span>

<span data-ttu-id="d0390-150">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="d0390-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d0390-151">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="d0390-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d0390-152">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d0390-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d0390-153">O `Id` segmento é um inteiro ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="d0390-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d0390-154">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d0390-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d0390-155">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d0390-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d0390-156">Constraint</span><span class="sxs-lookup"><span data-stu-id="d0390-156">Constraint</span></span> | <span data-ttu-id="d0390-157">Exemplo</span><span class="sxs-lookup"><span data-stu-id="d0390-157">Example</span></span>           | <span data-ttu-id="d0390-158">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="d0390-158">Example Matches</span></span>                                                                  | <span data-ttu-id="d0390-159">Constante</span><span class="sxs-lookup"><span data-stu-id="d0390-159">Invariant</span></span><br><span data-ttu-id="d0390-160">culture</span><span class="sxs-lookup"><span data-stu-id="d0390-160">culture</span></span><br><span data-ttu-id="d0390-161">correspondência</span><span class="sxs-lookup"><span data-stu-id="d0390-161">matching</span></span> |
| ---
<span data-ttu-id="d0390-162">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-163">'Blazor'</span></span>
- <span data-ttu-id="d0390-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-164">'Identity'</span></span>
- <span data-ttu-id="d0390-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-166">'Razor'</span></span>
- <span data-ttu-id="d0390-167">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-168">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-169">'Blazor'</span></span>
- <span data-ttu-id="d0390-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-170">'Identity'</span></span>
- <span data-ttu-id="d0390-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-172">'Razor'</span></span>
- <span data-ttu-id="d0390-173">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-174">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-175">'Blazor'</span></span>
- <span data-ttu-id="d0390-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-176">'Identity'</span></span>
- <span data-ttu-id="d0390-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-178">'Razor'</span></span>
- <span data-ttu-id="d0390-179">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-179">'SignalR' uid:</span></span> 

<span data-ttu-id="d0390-180">----- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-181">'Blazor'</span></span>
- <span data-ttu-id="d0390-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-182">'Identity'</span></span>
- <span data-ttu-id="d0390-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-184">'Razor'</span></span>
- <span data-ttu-id="d0390-185">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-186">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-187">'Blazor'</span></span>
- <span data-ttu-id="d0390-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-188">'Identity'</span></span>
- <span data-ttu-id="d0390-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-190">'Razor'</span></span>
- <span data-ttu-id="d0390-191">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-192">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-193">'Blazor'</span></span>
- <span data-ttu-id="d0390-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-194">'Identity'</span></span>
- <span data-ttu-id="d0390-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-196">'Razor'</span></span>
- <span data-ttu-id="d0390-197">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-198">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-199">'Blazor'</span></span>
- <span data-ttu-id="d0390-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-200">'Identity'</span></span>
- <span data-ttu-id="d0390-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-202">'Razor'</span></span>
- <span data-ttu-id="d0390-203">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-204">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-205">'Blazor'</span></span>
- <span data-ttu-id="d0390-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-206">'Identity'</span></span>
- <span data-ttu-id="d0390-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-208">'Razor'</span></span>
- <span data-ttu-id="d0390-209">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-210">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-211">'Blazor'</span></span>
- <span data-ttu-id="d0390-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-212">'Identity'</span></span>
- <span data-ttu-id="d0390-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-214">'Razor'</span></span>
- <span data-ttu-id="d0390-215">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-215">'SignalR' uid:</span></span> 

<span data-ttu-id="d0390-216">--------- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-217">'Blazor'</span></span>
- <span data-ttu-id="d0390-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-218">'Identity'</span></span>
- <span data-ttu-id="d0390-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-220">'Razor'</span></span>
- <span data-ttu-id="d0390-221">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-222">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-223">'Blazor'</span></span>
- <span data-ttu-id="d0390-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-224">'Identity'</span></span>
- <span data-ttu-id="d0390-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-226">'Razor'</span></span>
- <span data-ttu-id="d0390-227">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-228">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-229">'Blazor'</span></span>
- <span data-ttu-id="d0390-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-230">'Identity'</span></span>
- <span data-ttu-id="d0390-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-232">'Razor'</span></span>
- <span data-ttu-id="d0390-233">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-234">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-235">'Blazor'</span></span>
- <span data-ttu-id="d0390-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-236">'Identity'</span></span>
- <span data-ttu-id="d0390-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-238">'Razor'</span></span>
- <span data-ttu-id="d0390-239">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-240">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-241">'Blazor'</span></span>
- <span data-ttu-id="d0390-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-242">'Identity'</span></span>
- <span data-ttu-id="d0390-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-244">'Razor'</span></span>
- <span data-ttu-id="d0390-245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-246">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-247">'Blazor'</span></span>
- <span data-ttu-id="d0390-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-248">'Identity'</span></span>
- <span data-ttu-id="d0390-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-250">'Razor'</span></span>
- <span data-ttu-id="d0390-251">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-252">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-253">'Blazor'</span></span>
- <span data-ttu-id="d0390-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-254">'Identity'</span></span>
- <span data-ttu-id="d0390-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-256">'Razor'</span></span>
- <span data-ttu-id="d0390-257">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-258">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-259">'Blazor'</span></span>
- <span data-ttu-id="d0390-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-260">'Identity'</span></span>
- <span data-ttu-id="d0390-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-262">'Razor'</span></span>
- <span data-ttu-id="d0390-263">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-264">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-265">'Blazor'</span></span>
- <span data-ttu-id="d0390-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-266">'Identity'</span></span>
- <span data-ttu-id="d0390-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-268">'Razor'</span></span>
- <span data-ttu-id="d0390-269">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-270">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-271">'Blazor'</span></span>
- <span data-ttu-id="d0390-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-272">'Identity'</span></span>
- <span data-ttu-id="d0390-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-274">'Razor'</span></span>
- <span data-ttu-id="d0390-275">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-276">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-277">'Blazor'</span></span>
- <span data-ttu-id="d0390-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-278">'Identity'</span></span>
- <span data-ttu-id="d0390-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-280">'Razor'</span></span>
- <span data-ttu-id="d0390-281">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-282">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-283">'Blazor'</span></span>
- <span data-ttu-id="d0390-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-284">'Identity'</span></span>
- <span data-ttu-id="d0390-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-286">'Razor'</span></span>
- <span data-ttu-id="d0390-287">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-288">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-289">'Blazor'</span></span>
- <span data-ttu-id="d0390-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-290">'Identity'</span></span>
- <span data-ttu-id="d0390-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-292">'Razor'</span></span>
- <span data-ttu-id="d0390-293">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-294">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-295">'Blazor'</span></span>
- <span data-ttu-id="d0390-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-296">'Identity'</span></span>
- <span data-ttu-id="d0390-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-298">'Razor'</span></span>
- <span data-ttu-id="d0390-299">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-300">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-301">'Blazor'</span></span>
- <span data-ttu-id="d0390-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-302">'Identity'</span></span>
- <span data-ttu-id="d0390-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-304">'Razor'</span></span>
- <span data-ttu-id="d0390-305">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-306">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-307">'Blazor'</span></span>
- <span data-ttu-id="d0390-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-308">'Identity'</span></span>
- <span data-ttu-id="d0390-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-310">'Razor'</span></span>
- <span data-ttu-id="d0390-311">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-312">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-313">'Blazor'</span></span>
- <span data-ttu-id="d0390-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-314">'Identity'</span></span>
- <span data-ttu-id="d0390-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-316">'Razor'</span></span>
- <span data-ttu-id="d0390-317">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-318">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-319">'Blazor'</span></span>
- <span data-ttu-id="d0390-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-320">'Identity'</span></span>
- <span data-ttu-id="d0390-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-322">'Razor'</span></span>
- <span data-ttu-id="d0390-323">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-324">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-325">'Blazor'</span></span>
- <span data-ttu-id="d0390-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-326">'Identity'</span></span>
- <span data-ttu-id="d0390-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-328">'Razor'</span></span>
- <span data-ttu-id="d0390-329">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-330">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-331">'Blazor'</span></span>
- <span data-ttu-id="d0390-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-332">'Identity'</span></span>
- <span data-ttu-id="d0390-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-334">'Razor'</span></span>
- <span data-ttu-id="d0390-335">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-336">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-337">'Blazor'</span></span>
- <span data-ttu-id="d0390-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-338">'Identity'</span></span>
- <span data-ttu-id="d0390-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-340">'Razor'</span></span>
- <span data-ttu-id="d0390-341">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-342">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-343">'Blazor'</span></span>
- <span data-ttu-id="d0390-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-344">'Identity'</span></span>
- <span data-ttu-id="d0390-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-346">'Razor'</span></span>
- <span data-ttu-id="d0390-347">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-348">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-349">'Blazor'</span></span>
- <span data-ttu-id="d0390-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-350">'Identity'</span></span>
- <span data-ttu-id="d0390-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-352">'Razor'</span></span>
- <span data-ttu-id="d0390-353">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-354">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-355">'Blazor'</span></span>
- <span data-ttu-id="d0390-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-356">'Identity'</span></span>
- <span data-ttu-id="d0390-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-358">'Razor'</span></span>
- <span data-ttu-id="d0390-359">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-360">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-361">'Blazor'</span></span>
- <span data-ttu-id="d0390-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-362">'Identity'</span></span>
- <span data-ttu-id="d0390-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-364">'Razor'</span></span>
- <span data-ttu-id="d0390-365">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-366">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-367">'Blazor'</span></span>
- <span data-ttu-id="d0390-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-368">'Identity'</span></span>
- <span data-ttu-id="d0390-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-370">'Razor'</span></span>
- <span data-ttu-id="d0390-371">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-372">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-373">'Blazor'</span></span>
- <span data-ttu-id="d0390-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-374">'Identity'</span></span>
- <span data-ttu-id="d0390-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-376">'Razor'</span></span>
- <span data-ttu-id="d0390-377">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-378">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-379">'Blazor'</span></span>
- <span data-ttu-id="d0390-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-380">'Identity'</span></span>
- <span data-ttu-id="d0390-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-382">'Razor'</span></span>
- <span data-ttu-id="d0390-383">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-384">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-385">'Blazor'</span></span>
- <span data-ttu-id="d0390-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-386">'Identity'</span></span>
- <span data-ttu-id="d0390-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-388">'Razor'</span></span>
- <span data-ttu-id="d0390-389">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-390">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-391">'Blazor'</span></span>
- <span data-ttu-id="d0390-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-392">'Identity'</span></span>
- <span data-ttu-id="d0390-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-394">'Razor'</span></span>
- <span data-ttu-id="d0390-395">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-396">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-397">'Blazor'</span></span>
- <span data-ttu-id="d0390-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-398">'Identity'</span></span>
- <span data-ttu-id="d0390-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-400">'Razor'</span></span>
- <span data-ttu-id="d0390-401">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-402">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-403">'Blazor'</span></span>
- <span data-ttu-id="d0390-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-404">'Identity'</span></span>
- <span data-ttu-id="d0390-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-406">'Razor'</span></span>
- <span data-ttu-id="d0390-407">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-408">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-409">'Blazor'</span></span>
- <span data-ttu-id="d0390-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-410">'Identity'</span></span>
- <span data-ttu-id="d0390-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-412">'Razor'</span></span>
- <span data-ttu-id="d0390-413">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-414">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-415">'Blazor'</span></span>
- <span data-ttu-id="d0390-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-416">'Identity'</span></span>
- <span data-ttu-id="d0390-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-418">'Razor'</span></span>
- <span data-ttu-id="d0390-419">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-420">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-421">'Blazor'</span></span>
- <span data-ttu-id="d0390-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-422">'Identity'</span></span>
- <span data-ttu-id="d0390-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-424">'Razor'</span></span>
- <span data-ttu-id="d0390-425">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-426">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-427">'Blazor'</span></span>
- <span data-ttu-id="d0390-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-428">'Identity'</span></span>
- <span data-ttu-id="d0390-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-430">'Razor'</span></span>
- <span data-ttu-id="d0390-431">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-432">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-433">'Blazor'</span></span>
- <span data-ttu-id="d0390-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-434">'Identity'</span></span>
- <span data-ttu-id="d0390-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-436">'Razor'</span></span>
- <span data-ttu-id="d0390-437">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-438">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-439">'Blazor'</span></span>
- <span data-ttu-id="d0390-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-440">'Identity'</span></span>
- <span data-ttu-id="d0390-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-442">'Razor'</span></span>
- <span data-ttu-id="d0390-443">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-443">'SignalR' uid:</span></span> 

<span data-ttu-id="d0390-444">---------------------------------------- | :---Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-445">'Blazor'</span></span>
- <span data-ttu-id="d0390-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-446">'Identity'</span></span>
- <span data-ttu-id="d0390-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-448">'Razor'</span></span>
- <span data-ttu-id="d0390-449">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-450">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-451">'Blazor'</span></span>
- <span data-ttu-id="d0390-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-452">'Identity'</span></span>
- <span data-ttu-id="d0390-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-454">'Razor'</span></span>
- <span data-ttu-id="d0390-455">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-456">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-457">'Blazor'</span></span>
- <span data-ttu-id="d0390-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-458">'Identity'</span></span>
- <span data-ttu-id="d0390-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-460">'Razor'</span></span>
- <span data-ttu-id="d0390-461">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-462">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-463">'Blazor'</span></span>
- <span data-ttu-id="d0390-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-464">'Identity'</span></span>
- <span data-ttu-id="d0390-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-466">'Razor'</span></span>
- <span data-ttu-id="d0390-467">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-468">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-469">'Blazor'</span></span>
- <span data-ttu-id="d0390-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-470">'Identity'</span></span>
- <span data-ttu-id="d0390-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-472">'Razor'</span></span>
- <span data-ttu-id="d0390-473">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-474">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-475">'Blazor'</span></span>
- <span data-ttu-id="d0390-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-476">'Identity'</span></span>
- <span data-ttu-id="d0390-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-478">'Razor'</span></span>
- <span data-ttu-id="d0390-479">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-480">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-481">'Blazor'</span></span>
- <span data-ttu-id="d0390-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-482">'Identity'</span></span>
- <span data-ttu-id="d0390-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-484">'Razor'</span></span>
- <span data-ttu-id="d0390-485">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-486">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-487">'Blazor'</span></span>
- <span data-ttu-id="d0390-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-488">'Identity'</span></span>
- <span data-ttu-id="d0390-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-490">'Razor'</span></span>
- <span data-ttu-id="d0390-491">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-492">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-493">'Blazor'</span></span>
- <span data-ttu-id="d0390-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-494">'Identity'</span></span>
- <span data-ttu-id="d0390-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-496">'Razor'</span></span>
- <span data-ttu-id="d0390-497">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-498">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-499">'Blazor'</span></span>
- <span data-ttu-id="d0390-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-500">'Identity'</span></span>
- <span data-ttu-id="d0390-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-502">'Razor'</span></span>
- <span data-ttu-id="d0390-503">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-504">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-505">'Blazor'</span></span>
- <span data-ttu-id="d0390-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-506">'Identity'</span></span>
- <span data-ttu-id="d0390-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-508">'Razor'</span></span>
- <span data-ttu-id="d0390-509">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-510">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-511">'Blazor'</span></span>
- <span data-ttu-id="d0390-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-512">'Identity'</span></span>
- <span data-ttu-id="d0390-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-514">'Razor'</span></span>
- <span data-ttu-id="d0390-515">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-516">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-517">'Blazor'</span></span>
- <span data-ttu-id="d0390-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-518">'Identity'</span></span>
- <span data-ttu-id="d0390-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-520">'Razor'</span></span>
- <span data-ttu-id="d0390-521">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-521">'SignalR' uid:</span></span> 

<span data-ttu-id="d0390-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Não | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sim | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sim | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sim | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sim | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Não | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sim | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sim |</span><span class="sxs-lookup"><span data-stu-id="d0390-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="d0390-523">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="d0390-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="d0390-524">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="d0390-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d0390-525">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="d0390-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d0390-526">Em Blazor aplicativos de servidor, a rota padrão em *_Host. cshtml* é `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="d0390-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="d0390-527">Uma URL de solicitação que contém um ponto ( `.` ) não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="d0390-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="d0390-528">Um Blazor aplicativo retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="d0390-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="d0390-529">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="d0390-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="d0390-530">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="d0390-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="d0390-531">Sintaxe *catch-all* de asterisco duplo ( `**` ) para capturar o caminho entre vários limites de pasta sem barras invertidas de codificação ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="d0390-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="d0390-532">`path`nome do parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="d0390-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="d0390-533">A sintaxe de parâmetro *catch-all* ( `*` / `**` ) **não** tem suporte em Razor componentes (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="d0390-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="d0390-534">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d0390-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="d0390-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="d0390-535">NavLink component</span></span>

<span data-ttu-id="d0390-536">Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="d0390-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d0390-537">Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="d0390-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d0390-538">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="d0390-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="d0390-539">O componente a seguir `NavMenu` cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:</span><span class="sxs-lookup"><span data-stu-id="d0390-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d0390-540">Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="d0390-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d0390-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="d0390-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d0390-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*padrão*): o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="d0390-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d0390-543">No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="d0390-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d0390-544">O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="d0390-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="d0390-545"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="d0390-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d0390-546">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="d0390-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="d0390-547">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="d0390-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d0390-548">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="d0390-548">URI and navigation state helpers</span></span>

<span data-ttu-id="d0390-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="d0390-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d0390-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d0390-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d0390-551">Membro</span><span class="sxs-lookup"><span data-stu-id="d0390-551">Member</span></span> | <span data-ttu-id="d0390-552">Descrição</span><span class="sxs-lookup"><span data-stu-id="d0390-552">Description</span></span> |
| ---
<span data-ttu-id="d0390-553">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-554">'Blazor'</span></span>
- <span data-ttu-id="d0390-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-555">'Identity'</span></span>
- <span data-ttu-id="d0390-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-557">'Razor'</span></span>
- <span data-ttu-id="d0390-558">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-558">'SignalR' uid:</span></span> 

<span data-ttu-id="d0390-559">--- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-560">'Blazor'</span></span>
- <span data-ttu-id="d0390-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-561">'Identity'</span></span>
- <span data-ttu-id="d0390-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-563">'Razor'</span></span>
- <span data-ttu-id="d0390-564">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-565">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-566">'Blazor'</span></span>
- <span data-ttu-id="d0390-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-567">'Identity'</span></span>
- <span data-ttu-id="d0390-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-569">'Razor'</span></span>
- <span data-ttu-id="d0390-570">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d0390-571">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d0390-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d0390-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d0390-572">'Blazor'</span></span>
- <span data-ttu-id="d0390-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d0390-573">'Identity'</span></span>
- <span data-ttu-id="d0390-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d0390-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="d0390-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d0390-575">'Razor'</span></span>
- <span data-ttu-id="d0390-576">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="d0390-576">'SignalR' uid:</span></span> 

<span data-ttu-id="d0390-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="d0390-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="d0390-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="d0390-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d0390-579">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor servidor).</span><span class="sxs-lookup"><span data-stu-id="d0390-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="d0390-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="d0390-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="d0390-581">Se `forceLoad` for `true` :</span><span class="sxs-lookup"><span data-stu-id="d0390-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d0390-582">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="d0390-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d0390-583">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d0390-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="d0390-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="d0390-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="d0390-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="d0390-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="d0390-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="d0390-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d0390-587">O componente a seguir navega para o componente do aplicativo `Counter` quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="d0390-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="d0390-588">O componente a seguir manipula um evento de alteração de local definindo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d0390-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d0390-589">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="d0390-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="d0390-590">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="d0390-590">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="d0390-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornece as seguintes informações sobre o evento:</span><span class="sxs-lookup"><span data-stu-id="d0390-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="d0390-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="d0390-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="d0390-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , Blazor interceptou a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="d0390-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="d0390-594">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="d0390-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="d0390-595">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="d0390-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
