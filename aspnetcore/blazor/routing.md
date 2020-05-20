---
<span data-ttu-id="c0343-101">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-102">'Blazor'</span></span>
- <span data-ttu-id="c0343-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-103">'Identity'</span></span>
- <span data-ttu-id="c0343-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-105">'Razor'</span></span>
- <span data-ttu-id="c0343-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="c0343-107">Roteamento de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c0343-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="c0343-108">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c0343-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c0343-109">Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c0343-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="c0343-110">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0343-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="c0343-111">O servidor é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="c0343-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="c0343-112">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o `MapBlazorHub` no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c0343-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="c0343-113">A configuração mais típica é rotear todas as solicitações para uma Razor página, que atua como o host da parte do lado do servidor do Blazor aplicativo do servidor.</span><span class="sxs-lookup"><span data-stu-id="c0343-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="c0343-114">Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c0343-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="c0343-115">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="c0343-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="c0343-116">A rota de fallback é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="c0343-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="c0343-117">Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo do Blazor servidor.</span><span class="sxs-lookup"><span data-stu-id="c0343-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="c0343-118">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="c0343-118">Route templates</span></span>

<span data-ttu-id="c0343-119">O `Router` componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="c0343-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="c0343-120">O `Router` componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="c0343-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="c0343-121">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a classe gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="c0343-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="c0343-122">Em tempo de execução, o `RouteView` componente:</span><span class="sxs-lookup"><span data-stu-id="c0343-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="c0343-123">Recebe o `RouteData` do `Router` junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="c0343-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="c0343-124">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="c0343-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="c0343-125">Opcionalmente, você pode especificar um `DefaultLayout` parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="c0343-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="c0343-126">Os Blazor modelos padrão especificam o `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="c0343-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="c0343-127">*MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="c0343-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="c0343-128">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c0343-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="c0343-129">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="c0343-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c0343-130">O componente a seguir responde a solicitações para o `/BlazorRoute` e o `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="c0343-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="c0343-131">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu arquivo *wwwroot/index.html* ( Blazor Webassembly) ou *páginas/_Host arquivo. cshtml* ( Blazor servidor) com o caminho base do aplicativo especificado no `href` atributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="c0343-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="c0343-132">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="c0343-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="c0343-133">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="c0343-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="c0343-134">O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="c0343-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="c0343-135">No arquivo *app. Razor* , defina conteúdo personalizado no `NotFound` parâmetro de modelo do `Router` componente:</span><span class="sxs-lookup"><span data-stu-id="c0343-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="c0343-136">O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="c0343-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="c0343-137">Para aplicar um layout padrão ao `NotFound` conteúdo, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="c0343-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="c0343-138">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="c0343-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="c0343-139">Use o `AdditionalAssemblies` parâmetro para especificar assemblies adicionais para o `Router` componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="c0343-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="c0343-140">Os assemblies especificados são considerados além do `AppAssembly` assembly especificado.</span><span class="sxs-lookup"><span data-stu-id="c0343-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="c0343-141">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="c0343-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="c0343-142">O exemplo a seguir `AdditionalAssemblies` resulta no suporte de roteamento para `Component1` :</span><span class="sxs-lookup"><span data-stu-id="c0343-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="c0343-143">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="c0343-143">Route parameters</span></span>

<span data-ttu-id="c0343-144">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="c0343-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="c0343-145">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="c0343-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="c0343-146">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="c0343-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="c0343-147">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="c0343-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c0343-148">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="c0343-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="c0343-149">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="c0343-149">Route constraints</span></span>

<span data-ttu-id="c0343-150">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="c0343-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="c0343-151">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="c0343-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="c0343-152">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="c0343-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="c0343-153">O `Id` segmento é um inteiro ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="c0343-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="c0343-154">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="c0343-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="c0343-155">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c0343-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="c0343-156">Constraint</span><span class="sxs-lookup"><span data-stu-id="c0343-156">Constraint</span></span> | <span data-ttu-id="c0343-157">Exemplo</span><span class="sxs-lookup"><span data-stu-id="c0343-157">Example</span></span>           | <span data-ttu-id="c0343-158">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="c0343-158">Example Matches</span></span>                                                                  | <span data-ttu-id="c0343-159">Constante</span><span class="sxs-lookup"><span data-stu-id="c0343-159">Invariant</span></span><br><span data-ttu-id="c0343-160">culture</span><span class="sxs-lookup"><span data-stu-id="c0343-160">culture</span></span><br><span data-ttu-id="c0343-161">correspondência</span><span class="sxs-lookup"><span data-stu-id="c0343-161">matching</span></span> |
| ---
<span data-ttu-id="c0343-162">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-163">'Blazor'</span></span>
- <span data-ttu-id="c0343-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-164">'Identity'</span></span>
- <span data-ttu-id="c0343-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-166">'Razor'</span></span>
- <span data-ttu-id="c0343-167">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-168">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-169">'Blazor'</span></span>
- <span data-ttu-id="c0343-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-170">'Identity'</span></span>
- <span data-ttu-id="c0343-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-172">'Razor'</span></span>
- <span data-ttu-id="c0343-173">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-174">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-175">'Blazor'</span></span>
- <span data-ttu-id="c0343-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-176">'Identity'</span></span>
- <span data-ttu-id="c0343-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-178">'Razor'</span></span>
- <span data-ttu-id="c0343-179">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-179">'SignalR' uid:</span></span> 

<span data-ttu-id="c0343-180">----- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-181">'Blazor'</span></span>
- <span data-ttu-id="c0343-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-182">'Identity'</span></span>
- <span data-ttu-id="c0343-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-184">'Razor'</span></span>
- <span data-ttu-id="c0343-185">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-186">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-187">'Blazor'</span></span>
- <span data-ttu-id="c0343-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-188">'Identity'</span></span>
- <span data-ttu-id="c0343-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-190">'Razor'</span></span>
- <span data-ttu-id="c0343-191">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-192">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-193">'Blazor'</span></span>
- <span data-ttu-id="c0343-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-194">'Identity'</span></span>
- <span data-ttu-id="c0343-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-196">'Razor'</span></span>
- <span data-ttu-id="c0343-197">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-198">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-199">'Blazor'</span></span>
- <span data-ttu-id="c0343-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-200">'Identity'</span></span>
- <span data-ttu-id="c0343-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-202">'Razor'</span></span>
- <span data-ttu-id="c0343-203">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-204">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-205">'Blazor'</span></span>
- <span data-ttu-id="c0343-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-206">'Identity'</span></span>
- <span data-ttu-id="c0343-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-208">'Razor'</span></span>
- <span data-ttu-id="c0343-209">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-210">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-211">'Blazor'</span></span>
- <span data-ttu-id="c0343-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-212">'Identity'</span></span>
- <span data-ttu-id="c0343-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-214">'Razor'</span></span>
- <span data-ttu-id="c0343-215">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-215">'SignalR' uid:</span></span> 

<span data-ttu-id="c0343-216">--------- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-217">'Blazor'</span></span>
- <span data-ttu-id="c0343-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-218">'Identity'</span></span>
- <span data-ttu-id="c0343-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-220">'Razor'</span></span>
- <span data-ttu-id="c0343-221">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-222">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-223">'Blazor'</span></span>
- <span data-ttu-id="c0343-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-224">'Identity'</span></span>
- <span data-ttu-id="c0343-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-226">'Razor'</span></span>
- <span data-ttu-id="c0343-227">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-228">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-229">'Blazor'</span></span>
- <span data-ttu-id="c0343-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-230">'Identity'</span></span>
- <span data-ttu-id="c0343-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-232">'Razor'</span></span>
- <span data-ttu-id="c0343-233">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-234">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-235">'Blazor'</span></span>
- <span data-ttu-id="c0343-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-236">'Identity'</span></span>
- <span data-ttu-id="c0343-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-238">'Razor'</span></span>
- <span data-ttu-id="c0343-239">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-240">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-241">'Blazor'</span></span>
- <span data-ttu-id="c0343-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-242">'Identity'</span></span>
- <span data-ttu-id="c0343-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-244">'Razor'</span></span>
- <span data-ttu-id="c0343-245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-246">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-247">'Blazor'</span></span>
- <span data-ttu-id="c0343-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-248">'Identity'</span></span>
- <span data-ttu-id="c0343-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-250">'Razor'</span></span>
- <span data-ttu-id="c0343-251">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-252">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-253">'Blazor'</span></span>
- <span data-ttu-id="c0343-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-254">'Identity'</span></span>
- <span data-ttu-id="c0343-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-256">'Razor'</span></span>
- <span data-ttu-id="c0343-257">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-258">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-259">'Blazor'</span></span>
- <span data-ttu-id="c0343-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-260">'Identity'</span></span>
- <span data-ttu-id="c0343-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-262">'Razor'</span></span>
- <span data-ttu-id="c0343-263">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-264">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-265">'Blazor'</span></span>
- <span data-ttu-id="c0343-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-266">'Identity'</span></span>
- <span data-ttu-id="c0343-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-268">'Razor'</span></span>
- <span data-ttu-id="c0343-269">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-270">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-271">'Blazor'</span></span>
- <span data-ttu-id="c0343-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-272">'Identity'</span></span>
- <span data-ttu-id="c0343-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-274">'Razor'</span></span>
- <span data-ttu-id="c0343-275">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-276">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-277">'Blazor'</span></span>
- <span data-ttu-id="c0343-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-278">'Identity'</span></span>
- <span data-ttu-id="c0343-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-280">'Razor'</span></span>
- <span data-ttu-id="c0343-281">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-282">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-283">'Blazor'</span></span>
- <span data-ttu-id="c0343-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-284">'Identity'</span></span>
- <span data-ttu-id="c0343-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-286">'Razor'</span></span>
- <span data-ttu-id="c0343-287">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-288">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-289">'Blazor'</span></span>
- <span data-ttu-id="c0343-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-290">'Identity'</span></span>
- <span data-ttu-id="c0343-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-292">'Razor'</span></span>
- <span data-ttu-id="c0343-293">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-294">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-295">'Blazor'</span></span>
- <span data-ttu-id="c0343-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-296">'Identity'</span></span>
- <span data-ttu-id="c0343-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-298">'Razor'</span></span>
- <span data-ttu-id="c0343-299">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-300">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-301">'Blazor'</span></span>
- <span data-ttu-id="c0343-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-302">'Identity'</span></span>
- <span data-ttu-id="c0343-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-304">'Razor'</span></span>
- <span data-ttu-id="c0343-305">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-306">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-307">'Blazor'</span></span>
- <span data-ttu-id="c0343-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-308">'Identity'</span></span>
- <span data-ttu-id="c0343-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-310">'Razor'</span></span>
- <span data-ttu-id="c0343-311">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-312">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-313">'Blazor'</span></span>
- <span data-ttu-id="c0343-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-314">'Identity'</span></span>
- <span data-ttu-id="c0343-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-316">'Razor'</span></span>
- <span data-ttu-id="c0343-317">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-318">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-319">'Blazor'</span></span>
- <span data-ttu-id="c0343-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-320">'Identity'</span></span>
- <span data-ttu-id="c0343-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-322">'Razor'</span></span>
- <span data-ttu-id="c0343-323">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-324">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-325">'Blazor'</span></span>
- <span data-ttu-id="c0343-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-326">'Identity'</span></span>
- <span data-ttu-id="c0343-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-328">'Razor'</span></span>
- <span data-ttu-id="c0343-329">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-330">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-331">'Blazor'</span></span>
- <span data-ttu-id="c0343-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-332">'Identity'</span></span>
- <span data-ttu-id="c0343-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-334">'Razor'</span></span>
- <span data-ttu-id="c0343-335">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-336">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-337">'Blazor'</span></span>
- <span data-ttu-id="c0343-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-338">'Identity'</span></span>
- <span data-ttu-id="c0343-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-340">'Razor'</span></span>
- <span data-ttu-id="c0343-341">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-342">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-343">'Blazor'</span></span>
- <span data-ttu-id="c0343-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-344">'Identity'</span></span>
- <span data-ttu-id="c0343-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-346">'Razor'</span></span>
- <span data-ttu-id="c0343-347">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-348">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-349">'Blazor'</span></span>
- <span data-ttu-id="c0343-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-350">'Identity'</span></span>
- <span data-ttu-id="c0343-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-352">'Razor'</span></span>
- <span data-ttu-id="c0343-353">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-354">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-355">'Blazor'</span></span>
- <span data-ttu-id="c0343-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-356">'Identity'</span></span>
- <span data-ttu-id="c0343-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-358">'Razor'</span></span>
- <span data-ttu-id="c0343-359">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-360">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-361">'Blazor'</span></span>
- <span data-ttu-id="c0343-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-362">'Identity'</span></span>
- <span data-ttu-id="c0343-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-364">'Razor'</span></span>
- <span data-ttu-id="c0343-365">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-366">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-367">'Blazor'</span></span>
- <span data-ttu-id="c0343-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-368">'Identity'</span></span>
- <span data-ttu-id="c0343-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-370">'Razor'</span></span>
- <span data-ttu-id="c0343-371">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-372">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-373">'Blazor'</span></span>
- <span data-ttu-id="c0343-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-374">'Identity'</span></span>
- <span data-ttu-id="c0343-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-376">'Razor'</span></span>
- <span data-ttu-id="c0343-377">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-378">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-379">'Blazor'</span></span>
- <span data-ttu-id="c0343-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-380">'Identity'</span></span>
- <span data-ttu-id="c0343-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-382">'Razor'</span></span>
- <span data-ttu-id="c0343-383">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-384">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-385">'Blazor'</span></span>
- <span data-ttu-id="c0343-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-386">'Identity'</span></span>
- <span data-ttu-id="c0343-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-388">'Razor'</span></span>
- <span data-ttu-id="c0343-389">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-390">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-391">'Blazor'</span></span>
- <span data-ttu-id="c0343-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-392">'Identity'</span></span>
- <span data-ttu-id="c0343-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-394">'Razor'</span></span>
- <span data-ttu-id="c0343-395">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-396">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-397">'Blazor'</span></span>
- <span data-ttu-id="c0343-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-398">'Identity'</span></span>
- <span data-ttu-id="c0343-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-400">'Razor'</span></span>
- <span data-ttu-id="c0343-401">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-402">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-403">'Blazor'</span></span>
- <span data-ttu-id="c0343-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-404">'Identity'</span></span>
- <span data-ttu-id="c0343-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-406">'Razor'</span></span>
- <span data-ttu-id="c0343-407">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-408">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-409">'Blazor'</span></span>
- <span data-ttu-id="c0343-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-410">'Identity'</span></span>
- <span data-ttu-id="c0343-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-412">'Razor'</span></span>
- <span data-ttu-id="c0343-413">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-414">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-415">'Blazor'</span></span>
- <span data-ttu-id="c0343-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-416">'Identity'</span></span>
- <span data-ttu-id="c0343-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-418">'Razor'</span></span>
- <span data-ttu-id="c0343-419">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-420">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-421">'Blazor'</span></span>
- <span data-ttu-id="c0343-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-422">'Identity'</span></span>
- <span data-ttu-id="c0343-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-424">'Razor'</span></span>
- <span data-ttu-id="c0343-425">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-426">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-427">'Blazor'</span></span>
- <span data-ttu-id="c0343-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-428">'Identity'</span></span>
- <span data-ttu-id="c0343-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-430">'Razor'</span></span>
- <span data-ttu-id="c0343-431">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-432">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-433">'Blazor'</span></span>
- <span data-ttu-id="c0343-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-434">'Identity'</span></span>
- <span data-ttu-id="c0343-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-436">'Razor'</span></span>
- <span data-ttu-id="c0343-437">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-438">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-439">'Blazor'</span></span>
- <span data-ttu-id="c0343-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-440">'Identity'</span></span>
- <span data-ttu-id="c0343-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-442">'Razor'</span></span>
- <span data-ttu-id="c0343-443">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-443">'SignalR' uid:</span></span> 

<span data-ttu-id="c0343-444">---------------------------------------- | :---Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-445">'Blazor'</span></span>
- <span data-ttu-id="c0343-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-446">'Identity'</span></span>
- <span data-ttu-id="c0343-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-448">'Razor'</span></span>
- <span data-ttu-id="c0343-449">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-450">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-451">'Blazor'</span></span>
- <span data-ttu-id="c0343-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-452">'Identity'</span></span>
- <span data-ttu-id="c0343-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-454">'Razor'</span></span>
- <span data-ttu-id="c0343-455">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-456">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-457">'Blazor'</span></span>
- <span data-ttu-id="c0343-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-458">'Identity'</span></span>
- <span data-ttu-id="c0343-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-460">'Razor'</span></span>
- <span data-ttu-id="c0343-461">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-462">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-463">'Blazor'</span></span>
- <span data-ttu-id="c0343-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-464">'Identity'</span></span>
- <span data-ttu-id="c0343-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-466">'Razor'</span></span>
- <span data-ttu-id="c0343-467">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-468">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-469">'Blazor'</span></span>
- <span data-ttu-id="c0343-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-470">'Identity'</span></span>
- <span data-ttu-id="c0343-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-472">'Razor'</span></span>
- <span data-ttu-id="c0343-473">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-474">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-475">'Blazor'</span></span>
- <span data-ttu-id="c0343-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-476">'Identity'</span></span>
- <span data-ttu-id="c0343-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-478">'Razor'</span></span>
- <span data-ttu-id="c0343-479">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-480">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-481">'Blazor'</span></span>
- <span data-ttu-id="c0343-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-482">'Identity'</span></span>
- <span data-ttu-id="c0343-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-484">'Razor'</span></span>
- <span data-ttu-id="c0343-485">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-486">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-487">'Blazor'</span></span>
- <span data-ttu-id="c0343-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-488">'Identity'</span></span>
- <span data-ttu-id="c0343-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-490">'Razor'</span></span>
- <span data-ttu-id="c0343-491">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-492">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-493">'Blazor'</span></span>
- <span data-ttu-id="c0343-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-494">'Identity'</span></span>
- <span data-ttu-id="c0343-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-496">'Razor'</span></span>
- <span data-ttu-id="c0343-497">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-498">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-499">'Blazor'</span></span>
- <span data-ttu-id="c0343-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-500">'Identity'</span></span>
- <span data-ttu-id="c0343-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-502">'Razor'</span></span>
- <span data-ttu-id="c0343-503">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-504">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-505">'Blazor'</span></span>
- <span data-ttu-id="c0343-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-506">'Identity'</span></span>
- <span data-ttu-id="c0343-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-508">'Razor'</span></span>
- <span data-ttu-id="c0343-509">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-510">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-511">'Blazor'</span></span>
- <span data-ttu-id="c0343-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-512">'Identity'</span></span>
- <span data-ttu-id="c0343-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-514">'Razor'</span></span>
- <span data-ttu-id="c0343-515">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-516">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-517">'Blazor'</span></span>
- <span data-ttu-id="c0343-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-518">'Identity'</span></span>
- <span data-ttu-id="c0343-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-520">'Razor'</span></span>
- <span data-ttu-id="c0343-521">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-521">'SignalR' uid:</span></span> 

<span data-ttu-id="c0343-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Não | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sim | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sim | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sim | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sim | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Não | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sim | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sim |</span><span class="sxs-lookup"><span data-stu-id="c0343-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="c0343-523">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="c0343-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="c0343-524">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="c0343-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="c0343-525">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="c0343-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="c0343-526">Em Blazor aplicativos de servidor, a rota padrão em *_Host. cshtml* é `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="c0343-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="c0343-527">Uma URL de solicitação que contém um ponto ( `.` ) não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="c0343-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="c0343-528">Um Blazor aplicativo retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="c0343-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="c0343-529">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="c0343-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="c0343-530">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="c0343-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="c0343-531">Sintaxe *catch-all* de asterisco duplo ( `**` ) para capturar o caminho entre vários limites de pasta sem barras invertidas de codificação ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="c0343-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="c0343-532">`path`nome do parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="c0343-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="c0343-533">A sintaxe de parâmetro *catch-all* ( `*` / `**` ) **não** tem suporte em Razor componentes (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="c0343-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="c0343-534">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c0343-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="c0343-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="c0343-535">NavLink component</span></span>

<span data-ttu-id="c0343-536">Use um `NavLink` componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="c0343-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="c0343-537">Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="c0343-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="c0343-538">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="c0343-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="c0343-539">O componente a seguir `NavMenu` cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra como usar `NavLink` componentes:</span><span class="sxs-lookup"><span data-stu-id="c0343-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="c0343-540">Há duas `NavLinkMatch` opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="c0343-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="c0343-541">`NavLinkMatch.All`&ndash;O `NavLink` está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="c0343-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="c0343-542">`NavLinkMatch.Prefix`(*padrão*) &ndash; O `NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="c0343-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="c0343-543">No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="c0343-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="c0343-544">O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="c0343-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="c0343-545">`NavLink`Atributos de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="c0343-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="c0343-546">No exemplo a seguir, o `NavLink` componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="c0343-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="c0343-547">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="c0343-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="c0343-548">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="c0343-548">URI and navigation state helpers</span></span>

<span data-ttu-id="c0343-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="c0343-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="c0343-550">`NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="c0343-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="c0343-551">Membro</span><span class="sxs-lookup"><span data-stu-id="c0343-551">Member</span></span> | <span data-ttu-id="c0343-552">Descrição</span><span class="sxs-lookup"><span data-stu-id="c0343-552">Description</span></span> |
| ---
<span data-ttu-id="c0343-553">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-554">'Blazor'</span></span>
- <span data-ttu-id="c0343-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-555">'Identity'</span></span>
- <span data-ttu-id="c0343-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-557">'Razor'</span></span>
- <span data-ttu-id="c0343-558">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-558">'SignalR' uid:</span></span> 

<span data-ttu-id="c0343-559">--- | título do---: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-560">'Blazor'</span></span>
- <span data-ttu-id="c0343-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-561">'Identity'</span></span>
- <span data-ttu-id="c0343-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-563">'Razor'</span></span>
- <span data-ttu-id="c0343-564">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-565">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-566">'Blazor'</span></span>
- <span data-ttu-id="c0343-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-567">'Identity'</span></span>
- <span data-ttu-id="c0343-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-569">'Razor'</span></span>
- <span data-ttu-id="c0343-570">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c0343-571">Título: ' ASP.NET Core Blazor Roteamento ' autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0343-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0343-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0343-572">'Blazor'</span></span>
- <span data-ttu-id="c0343-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0343-573">'Identity'</span></span>
- <span data-ttu-id="c0343-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0343-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0343-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0343-575">'Razor'</span></span>
- <span data-ttu-id="c0343-576">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c0343-576">'SignalR' uid:</span></span> 

<span data-ttu-id="c0343-577">------ | | URI | Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="c0343-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="c0343-578">| | BaseUri | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="c0343-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="c0343-579">Normalmente, `BaseUri` corresponde ao `href` atributo no elemento do documento `<base>` em *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor servidor).</span><span class="sxs-lookup"><span data-stu-id="c0343-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="c0343-580">| | NavigateTo | Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="c0343-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="c0343-581">Se `forceLoad` for `true` :</span><span class="sxs-lookup"><span data-stu-id="c0343-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="c0343-582">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="c0343-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="c0343-583">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="c0343-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="c0343-584">| | Localchanged | Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="c0343-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="c0343-585">| | ToAbsoluteUri | Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="c0343-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="c0343-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Dado um URI de base (por exemplo, um URI retornado anteriormente pelo `GetBaseUri` ), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="c0343-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="c0343-587">O componente a seguir navega para o componente do aplicativo `Counter` quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="c0343-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="c0343-588">O componente a seguir manipula um evento de local alterado.</span><span class="sxs-lookup"><span data-stu-id="c0343-588">The following component handles a location changed event.</span></span> <span data-ttu-id="c0343-589">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="c0343-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="c0343-590">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="c0343-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="c0343-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornece as seguintes informações sobre o evento:</span><span class="sxs-lookup"><span data-stu-id="c0343-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="c0343-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="c0343-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="c0343-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Se `true` , Blazor interceptar a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="c0343-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="c0343-594">Se `false` , [navigationmanager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="c0343-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="c0343-595">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="c0343-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
