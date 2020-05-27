---
<span data-ttu-id="fc41e-101">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-102">'Blazor'</span></span>
- <span data-ttu-id="fc41e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-103">'Identity'</span></span>
- <span data-ttu-id="fc41e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-105">'Razor'</span></span>
- <span data-ttu-id="fc41e-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="fc41e-107">Roteamento de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fc41e-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="fc41e-108">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc41e-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fc41e-109">Saiba como rotear solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="fc41e-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="fc41e-110">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc41e-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="fc41e-111">O servidor é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="fc41e-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="fc41e-112">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="fc41e-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="fc41e-113">A configuração mais típica é rotear todas as solicitações para uma Razor página, que atua como o host da parte do lado do servidor do Blazor aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="fc41e-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="fc41e-114">Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="fc41e-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="fc41e-115">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="fc41e-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="fc41e-116">A rota de fallback é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="fc41e-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="fc41e-117">Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo do Blazor servidor.</span><span class="sxs-lookup"><span data-stu-id="fc41e-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="fc41e-118">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="fc41e-118">Route templates</span></span>

<span data-ttu-id="fc41e-119">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="fc41e-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="fc41e-120">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="fc41e-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="fc41e-121">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a classe gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="fc41e-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="fc41e-122">Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="fc41e-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="fc41e-123">Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="fc41e-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="fc41e-124">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="fc41e-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="fc41e-125">Opcionalmente, você pode especificar um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="fc41e-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="fc41e-126">Os Blazor modelos padrão especificam o `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="fc41e-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="fc41e-127">*MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="fc41e-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="fc41e-128">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="fc41e-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="fc41e-129">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="fc41e-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="fc41e-130">O componente a seguir responde a solicitações para o `/BlazorRoute` e o `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="fc41e-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="fc41e-131">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu arquivo *wwwroot/index.html* ( Blazor Webassembly) ou *páginas/_Host arquivo. cshtml* ( Blazor servidor) com o caminho base do aplicativo especificado no `href` atributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="fc41e-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="fc41e-132">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="fc41e-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="fc41e-133">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="fc41e-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="fc41e-134">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="fc41e-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="fc41e-135">No arquivo *app. Razor* , defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parâmetro de modelo do <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:</span><span class="sxs-lookup"><span data-stu-id="fc41e-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="fc41e-136">O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="fc41e-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="fc41e-137">Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="fc41e-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="fc41e-138">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="fc41e-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="fc41e-139">Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="fc41e-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="fc41e-140">Os assemblies especificados são considerados além do `AppAssembly` assembly especificado.</span><span class="sxs-lookup"><span data-stu-id="fc41e-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="fc41e-141">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="fc41e-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="fc41e-142">O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` :</span><span class="sxs-lookup"><span data-stu-id="fc41e-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="fc41e-143">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="fc41e-143">Route parameters</span></span>

<span data-ttu-id="fc41e-144">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="fc41e-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="fc41e-145">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="fc41e-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="fc41e-146">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="fc41e-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="fc41e-147">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="fc41e-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="fc41e-148">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="fc41e-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="fc41e-149">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="fc41e-149">Route constraints</span></span>

<span data-ttu-id="fc41e-150">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="fc41e-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="fc41e-151">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="fc41e-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="fc41e-152">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="fc41e-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="fc41e-153">O `Id` segmento é um inteiro ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="fc41e-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="fc41e-154">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="fc41e-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="fc41e-155">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="fc41e-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="fc41e-156">Constraint</span><span class="sxs-lookup"><span data-stu-id="fc41e-156">Constraint</span></span> | <span data-ttu-id="fc41e-157">Exemplo</span><span class="sxs-lookup"><span data-stu-id="fc41e-157">Example</span></span>           | <span data-ttu-id="fc41e-158">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="fc41e-158">Example Matches</span></span>                                                                  | <span data-ttu-id="fc41e-159">Constante</span><span class="sxs-lookup"><span data-stu-id="fc41e-159">Invariant</span></span><br><span data-ttu-id="fc41e-160">culture</span><span class="sxs-lookup"><span data-stu-id="fc41e-160">culture</span></span><br><span data-ttu-id="fc41e-161">correspondência</span><span class="sxs-lookup"><span data-stu-id="fc41e-161">matching</span></span> |
| ---
<span data-ttu-id="fc41e-162">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-163">'Blazor'</span></span>
- <span data-ttu-id="fc41e-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-164">'Identity'</span></span>
- <span data-ttu-id="fc41e-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-166">'Razor'</span></span>
- <span data-ttu-id="fc41e-167">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-168">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-169">'Blazor'</span></span>
- <span data-ttu-id="fc41e-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-170">'Identity'</span></span>
- <span data-ttu-id="fc41e-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-172">'Razor'</span></span>
- <span data-ttu-id="fc41e-173">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-174">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-175">'Blazor'</span></span>
- <span data-ttu-id="fc41e-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-176">'Identity'</span></span>
- <span data-ttu-id="fc41e-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-178">'Razor'</span></span>
- <span data-ttu-id="fc41e-179">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-179">'SignalR' uid:</span></span> 

<span data-ttu-id="fc41e-180">----- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-181">'Blazor'</span></span>
- <span data-ttu-id="fc41e-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-182">'Identity'</span></span>
- <span data-ttu-id="fc41e-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-184">'Razor'</span></span>
- <span data-ttu-id="fc41e-185">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-186">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-187">'Blazor'</span></span>
- <span data-ttu-id="fc41e-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-188">'Identity'</span></span>
- <span data-ttu-id="fc41e-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-190">'Razor'</span></span>
- <span data-ttu-id="fc41e-191">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-192">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-193">'Blazor'</span></span>
- <span data-ttu-id="fc41e-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-194">'Identity'</span></span>
- <span data-ttu-id="fc41e-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-196">'Razor'</span></span>
- <span data-ttu-id="fc41e-197">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-198">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-199">'Blazor'</span></span>
- <span data-ttu-id="fc41e-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-200">'Identity'</span></span>
- <span data-ttu-id="fc41e-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-202">'Razor'</span></span>
- <span data-ttu-id="fc41e-203">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-204">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-205">'Blazor'</span></span>
- <span data-ttu-id="fc41e-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-206">'Identity'</span></span>
- <span data-ttu-id="fc41e-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-208">'Razor'</span></span>
- <span data-ttu-id="fc41e-209">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-210">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-211">'Blazor'</span></span>
- <span data-ttu-id="fc41e-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-212">'Identity'</span></span>
- <span data-ttu-id="fc41e-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-214">'Razor'</span></span>
- <span data-ttu-id="fc41e-215">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-215">'SignalR' uid:</span></span> 

<span data-ttu-id="fc41e-216">--------- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-217">'Blazor'</span></span>
- <span data-ttu-id="fc41e-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-218">'Identity'</span></span>
- <span data-ttu-id="fc41e-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-220">'Razor'</span></span>
- <span data-ttu-id="fc41e-221">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-222">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-223">'Blazor'</span></span>
- <span data-ttu-id="fc41e-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-224">'Identity'</span></span>
- <span data-ttu-id="fc41e-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-226">'Razor'</span></span>
- <span data-ttu-id="fc41e-227">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-228">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-229">'Blazor'</span></span>
- <span data-ttu-id="fc41e-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-230">'Identity'</span></span>
- <span data-ttu-id="fc41e-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-232">'Razor'</span></span>
- <span data-ttu-id="fc41e-233">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-234">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-235">'Blazor'</span></span>
- <span data-ttu-id="fc41e-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-236">'Identity'</span></span>
- <span data-ttu-id="fc41e-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-238">'Razor'</span></span>
- <span data-ttu-id="fc41e-239">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-240">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-241">'Blazor'</span></span>
- <span data-ttu-id="fc41e-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-242">'Identity'</span></span>
- <span data-ttu-id="fc41e-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-244">'Razor'</span></span>
- <span data-ttu-id="fc41e-245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-246">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-247">'Blazor'</span></span>
- <span data-ttu-id="fc41e-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-248">'Identity'</span></span>
- <span data-ttu-id="fc41e-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-250">'Razor'</span></span>
- <span data-ttu-id="fc41e-251">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-252">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-253">'Blazor'</span></span>
- <span data-ttu-id="fc41e-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-254">'Identity'</span></span>
- <span data-ttu-id="fc41e-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-256">'Razor'</span></span>
- <span data-ttu-id="fc41e-257">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-258">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-259">'Blazor'</span></span>
- <span data-ttu-id="fc41e-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-260">'Identity'</span></span>
- <span data-ttu-id="fc41e-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-262">'Razor'</span></span>
- <span data-ttu-id="fc41e-263">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-264">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-265">'Blazor'</span></span>
- <span data-ttu-id="fc41e-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-266">'Identity'</span></span>
- <span data-ttu-id="fc41e-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-268">'Razor'</span></span>
- <span data-ttu-id="fc41e-269">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-270">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-271">'Blazor'</span></span>
- <span data-ttu-id="fc41e-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-272">'Identity'</span></span>
- <span data-ttu-id="fc41e-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-274">'Razor'</span></span>
- <span data-ttu-id="fc41e-275">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-276">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-277">'Blazor'</span></span>
- <span data-ttu-id="fc41e-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-278">'Identity'</span></span>
- <span data-ttu-id="fc41e-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-280">'Razor'</span></span>
- <span data-ttu-id="fc41e-281">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-282">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-283">'Blazor'</span></span>
- <span data-ttu-id="fc41e-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-284">'Identity'</span></span>
- <span data-ttu-id="fc41e-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-286">'Razor'</span></span>
- <span data-ttu-id="fc41e-287">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-288">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-289">'Blazor'</span></span>
- <span data-ttu-id="fc41e-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-290">'Identity'</span></span>
- <span data-ttu-id="fc41e-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-292">'Razor'</span></span>
- <span data-ttu-id="fc41e-293">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-294">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-295">'Blazor'</span></span>
- <span data-ttu-id="fc41e-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-296">'Identity'</span></span>
- <span data-ttu-id="fc41e-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-298">'Razor'</span></span>
- <span data-ttu-id="fc41e-299">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-300">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-301">'Blazor'</span></span>
- <span data-ttu-id="fc41e-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-302">'Identity'</span></span>
- <span data-ttu-id="fc41e-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-304">'Razor'</span></span>
- <span data-ttu-id="fc41e-305">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-306">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-307">'Blazor'</span></span>
- <span data-ttu-id="fc41e-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-308">'Identity'</span></span>
- <span data-ttu-id="fc41e-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-310">'Razor'</span></span>
- <span data-ttu-id="fc41e-311">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-312">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-313">'Blazor'</span></span>
- <span data-ttu-id="fc41e-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-314">'Identity'</span></span>
- <span data-ttu-id="fc41e-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-316">'Razor'</span></span>
- <span data-ttu-id="fc41e-317">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-318">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-319">'Blazor'</span></span>
- <span data-ttu-id="fc41e-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-320">'Identity'</span></span>
- <span data-ttu-id="fc41e-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-322">'Razor'</span></span>
- <span data-ttu-id="fc41e-323">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-324">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-325">'Blazor'</span></span>
- <span data-ttu-id="fc41e-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-326">'Identity'</span></span>
- <span data-ttu-id="fc41e-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-328">'Razor'</span></span>
- <span data-ttu-id="fc41e-329">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-330">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-331">'Blazor'</span></span>
- <span data-ttu-id="fc41e-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-332">'Identity'</span></span>
- <span data-ttu-id="fc41e-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-334">'Razor'</span></span>
- <span data-ttu-id="fc41e-335">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-336">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-337">'Blazor'</span></span>
- <span data-ttu-id="fc41e-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-338">'Identity'</span></span>
- <span data-ttu-id="fc41e-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-340">'Razor'</span></span>
- <span data-ttu-id="fc41e-341">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-342">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-343">'Blazor'</span></span>
- <span data-ttu-id="fc41e-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-344">'Identity'</span></span>
- <span data-ttu-id="fc41e-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-346">'Razor'</span></span>
- <span data-ttu-id="fc41e-347">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-348">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-349">'Blazor'</span></span>
- <span data-ttu-id="fc41e-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-350">'Identity'</span></span>
- <span data-ttu-id="fc41e-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-352">'Razor'</span></span>
- <span data-ttu-id="fc41e-353">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-354">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-355">'Blazor'</span></span>
- <span data-ttu-id="fc41e-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-356">'Identity'</span></span>
- <span data-ttu-id="fc41e-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-358">'Razor'</span></span>
- <span data-ttu-id="fc41e-359">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-360">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-361">'Blazor'</span></span>
- <span data-ttu-id="fc41e-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-362">'Identity'</span></span>
- <span data-ttu-id="fc41e-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-364">'Razor'</span></span>
- <span data-ttu-id="fc41e-365">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-366">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-367">'Blazor'</span></span>
- <span data-ttu-id="fc41e-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-368">'Identity'</span></span>
- <span data-ttu-id="fc41e-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-370">'Razor'</span></span>
- <span data-ttu-id="fc41e-371">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-372">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-373">'Blazor'</span></span>
- <span data-ttu-id="fc41e-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-374">'Identity'</span></span>
- <span data-ttu-id="fc41e-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-376">'Razor'</span></span>
- <span data-ttu-id="fc41e-377">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-378">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-379">'Blazor'</span></span>
- <span data-ttu-id="fc41e-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-380">'Identity'</span></span>
- <span data-ttu-id="fc41e-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-382">'Razor'</span></span>
- <span data-ttu-id="fc41e-383">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-384">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-385">'Blazor'</span></span>
- <span data-ttu-id="fc41e-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-386">'Identity'</span></span>
- <span data-ttu-id="fc41e-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-388">'Razor'</span></span>
- <span data-ttu-id="fc41e-389">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-390">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-391">'Blazor'</span></span>
- <span data-ttu-id="fc41e-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-392">'Identity'</span></span>
- <span data-ttu-id="fc41e-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-394">'Razor'</span></span>
- <span data-ttu-id="fc41e-395">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-396">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-397">'Blazor'</span></span>
- <span data-ttu-id="fc41e-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-398">'Identity'</span></span>
- <span data-ttu-id="fc41e-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-400">'Razor'</span></span>
- <span data-ttu-id="fc41e-401">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-402">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-403">'Blazor'</span></span>
- <span data-ttu-id="fc41e-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-404">'Identity'</span></span>
- <span data-ttu-id="fc41e-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-406">'Razor'</span></span>
- <span data-ttu-id="fc41e-407">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-408">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-409">'Blazor'</span></span>
- <span data-ttu-id="fc41e-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-410">'Identity'</span></span>
- <span data-ttu-id="fc41e-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-412">'Razor'</span></span>
- <span data-ttu-id="fc41e-413">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-414">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-415">'Blazor'</span></span>
- <span data-ttu-id="fc41e-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-416">'Identity'</span></span>
- <span data-ttu-id="fc41e-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-418">'Razor'</span></span>
- <span data-ttu-id="fc41e-419">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-420">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-421">'Blazor'</span></span>
- <span data-ttu-id="fc41e-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-422">'Identity'</span></span>
- <span data-ttu-id="fc41e-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-424">'Razor'</span></span>
- <span data-ttu-id="fc41e-425">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-426">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-427">'Blazor'</span></span>
- <span data-ttu-id="fc41e-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-428">'Identity'</span></span>
- <span data-ttu-id="fc41e-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-430">'Razor'</span></span>
- <span data-ttu-id="fc41e-431">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-432">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-433">'Blazor'</span></span>
- <span data-ttu-id="fc41e-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-434">'Identity'</span></span>
- <span data-ttu-id="fc41e-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-436">'Razor'</span></span>
- <span data-ttu-id="fc41e-437">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-438">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-439">'Blazor'</span></span>
- <span data-ttu-id="fc41e-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-440">'Identity'</span></span>
- <span data-ttu-id="fc41e-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-442">'Razor'</span></span>
- <span data-ttu-id="fc41e-443">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-443">'SignalR' uid:</span></span> 

<span data-ttu-id="fc41e-444">---------------------------------------- | :---Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-445">'Blazor'</span></span>
- <span data-ttu-id="fc41e-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-446">'Identity'</span></span>
- <span data-ttu-id="fc41e-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-448">'Razor'</span></span>
- <span data-ttu-id="fc41e-449">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-450">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-451">'Blazor'</span></span>
- <span data-ttu-id="fc41e-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-452">'Identity'</span></span>
- <span data-ttu-id="fc41e-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-454">'Razor'</span></span>
- <span data-ttu-id="fc41e-455">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-456">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-457">'Blazor'</span></span>
- <span data-ttu-id="fc41e-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-458">'Identity'</span></span>
- <span data-ttu-id="fc41e-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-460">'Razor'</span></span>
- <span data-ttu-id="fc41e-461">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-462">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-463">'Blazor'</span></span>
- <span data-ttu-id="fc41e-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-464">'Identity'</span></span>
- <span data-ttu-id="fc41e-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-466">'Razor'</span></span>
- <span data-ttu-id="fc41e-467">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-468">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-469">'Blazor'</span></span>
- <span data-ttu-id="fc41e-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-470">'Identity'</span></span>
- <span data-ttu-id="fc41e-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-472">'Razor'</span></span>
- <span data-ttu-id="fc41e-473">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-474">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-475">'Blazor'</span></span>
- <span data-ttu-id="fc41e-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-476">'Identity'</span></span>
- <span data-ttu-id="fc41e-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-478">'Razor'</span></span>
- <span data-ttu-id="fc41e-479">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-480">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-481">'Blazor'</span></span>
- <span data-ttu-id="fc41e-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-482">'Identity'</span></span>
- <span data-ttu-id="fc41e-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-484">'Razor'</span></span>
- <span data-ttu-id="fc41e-485">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-486">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-487">'Blazor'</span></span>
- <span data-ttu-id="fc41e-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-488">'Identity'</span></span>
- <span data-ttu-id="fc41e-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-490">'Razor'</span></span>
- <span data-ttu-id="fc41e-491">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-492">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-493">'Blazor'</span></span>
- <span data-ttu-id="fc41e-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-494">'Identity'</span></span>
- <span data-ttu-id="fc41e-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-496">'Razor'</span></span>
- <span data-ttu-id="fc41e-497">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-498">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-499">'Blazor'</span></span>
- <span data-ttu-id="fc41e-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-500">'Identity'</span></span>
- <span data-ttu-id="fc41e-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-502">'Razor'</span></span>
- <span data-ttu-id="fc41e-503">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-504">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-505">'Blazor'</span></span>
- <span data-ttu-id="fc41e-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-506">'Identity'</span></span>
- <span data-ttu-id="fc41e-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-508">'Razor'</span></span>
- <span data-ttu-id="fc41e-509">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-510">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-511">'Blazor'</span></span>
- <span data-ttu-id="fc41e-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-512">'Identity'</span></span>
- <span data-ttu-id="fc41e-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-514">'Razor'</span></span>
- <span data-ttu-id="fc41e-515">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-516">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-517">'Blazor'</span></span>
- <span data-ttu-id="fc41e-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-518">'Identity'</span></span>
- <span data-ttu-id="fc41e-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-520">'Razor'</span></span>
- <span data-ttu-id="fc41e-521">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-521">'SignalR' uid:</span></span> 

<span data-ttu-id="fc41e-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Não | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sim | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sim | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sim | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sim | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Não | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sim | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sim |</span><span class="sxs-lookup"><span data-stu-id="fc41e-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="fc41e-523">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="fc41e-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="fc41e-524">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="fc41e-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="fc41e-525">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="fc41e-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="fc41e-526">Em Blazor aplicativos de servidor, a rota padrão em *_Host. cshtml* é `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="fc41e-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="fc41e-527">Uma URL de solicitação que contém um ponto ( `.` ) não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="fc41e-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="fc41e-528">Um Blazor aplicativo retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="fc41e-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="fc41e-529">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="fc41e-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="fc41e-530">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="fc41e-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="fc41e-531">Sintaxe *catch-all* de asterisco duplo ( `**` ) para capturar o caminho entre vários limites de pasta sem barras invertidas de codificação ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="fc41e-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="fc41e-532">`path`nome do parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="fc41e-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="fc41e-533">A sintaxe de parâmetro *catch-all* ( `*` / `**` ) **não** tem suporte em Razor componentes (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="fc41e-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="fc41e-534">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="fc41e-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="fc41e-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="fc41e-535">NavLink component</span></span>

<span data-ttu-id="fc41e-536">Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="fc41e-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="fc41e-537">Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="fc41e-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="fc41e-538">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="fc41e-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="fc41e-539">O componente a seguir `NavMenu` cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:</span><span class="sxs-lookup"><span data-stu-id="fc41e-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="fc41e-540">Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="fc41e-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="fc41e-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>&ndash;O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="fc41e-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType> &ndash; The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="fc41e-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*padrão*) &ndash; O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="fc41e-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*) &ndash; The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="fc41e-543">No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="fc41e-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="fc41e-544">O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="fc41e-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="fc41e-545"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="fc41e-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="fc41e-546">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="fc41e-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="fc41e-547">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="fc41e-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="fc41e-548">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="fc41e-548">URI and navigation state helpers</span></span>

<span data-ttu-id="fc41e-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="fc41e-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="fc41e-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="fc41e-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="fc41e-551">Membro</span><span class="sxs-lookup"><span data-stu-id="fc41e-551">Member</span></span> | <span data-ttu-id="fc41e-552">Descrição</span><span class="sxs-lookup"><span data-stu-id="fc41e-552">Description</span></span> |
| ---
<span data-ttu-id="fc41e-553">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-554">'Blazor'</span></span>
- <span data-ttu-id="fc41e-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-555">'Identity'</span></span>
- <span data-ttu-id="fc41e-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-557">'Razor'</span></span>
- <span data-ttu-id="fc41e-558">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-558">'SignalR' uid:</span></span> 

<span data-ttu-id="fc41e-559">--- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-560">'Blazor'</span></span>
- <span data-ttu-id="fc41e-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-561">'Identity'</span></span>
- <span data-ttu-id="fc41e-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-563">'Razor'</span></span>
- <span data-ttu-id="fc41e-564">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-565">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-566">'Blazor'</span></span>
- <span data-ttu-id="fc41e-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-567">'Identity'</span></span>
- <span data-ttu-id="fc41e-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-569">'Razor'</span></span>
- <span data-ttu-id="fc41e-570">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fc41e-571">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fc41e-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fc41e-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-572">'Blazor'</span></span>
- <span data-ttu-id="fc41e-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fc41e-573">'Identity'</span></span>
- <span data-ttu-id="fc41e-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fc41e-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="fc41e-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fc41e-575">'Razor'</span></span>
- <span data-ttu-id="fc41e-576">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="fc41e-576">'SignalR' uid:</span></span> 

<span data-ttu-id="fc41e-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="fc41e-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="fc41e-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="fc41e-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="fc41e-579">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor servidor).</span><span class="sxs-lookup"><span data-stu-id="fc41e-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="fc41e-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="fc41e-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="fc41e-581">Se `forceLoad` for `true` :</span><span class="sxs-lookup"><span data-stu-id="fc41e-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="fc41e-582">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="fc41e-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="fc41e-583">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="fc41e-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="fc41e-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="fc41e-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="fc41e-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="fc41e-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="fc41e-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="fc41e-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="fc41e-587">O componente a seguir navega para o componente do aplicativo `Counter` quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="fc41e-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="fc41e-588">O componente a seguir manipula um evento de alteração de local definindo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="fc41e-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="fc41e-589">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="fc41e-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="fc41e-590">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="fc41e-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="fc41e-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornece as seguintes informações sobre o evento:</span><span class="sxs-lookup"><span data-stu-id="fc41e-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="fc41e-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="fc41e-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="fc41e-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Se `true` , Blazor interceptar a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="fc41e-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="fc41e-594">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="fc41e-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="fc41e-595">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="fc41e-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
