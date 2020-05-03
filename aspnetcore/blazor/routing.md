---
title: Roteamento Blazor de ASP.NET Core
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 291ec33f951008df10f85336c7abd0b3d0a50bbc
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727713"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="3dd19-103">Roteamento de ASP.NET Core mais</span><span class="sxs-lookup"><span data-stu-id="3dd19-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="3dd19-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3dd19-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3dd19-105">Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em aplicativos mais incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="3dd19-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="3dd19-106">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dd19-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="3dd19-107">O servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="3dd19-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="3dd19-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` o no: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="3dd19-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="3dd19-109">A configuração mais típica é rotear todas as solicitações para uma página Razor, que atua como o host para a parte do lado do servidor do aplicativo de servidor mais grande.</span><span class="sxs-lookup"><span data-stu-id="3dd19-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="3dd19-110">Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3dd19-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="3dd19-111">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="3dd19-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="3dd19-112">A rota de fallback é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="3dd19-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="3dd19-113">Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="3dd19-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="3dd19-114">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="3dd19-114">Route templates</span></span>

<span data-ttu-id="3dd19-115">O `Router` componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="3dd19-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="3dd19-116">O `Router` componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="3dd19-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="3dd19-117">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a classe gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="3dd19-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="3dd19-118">Em tempo de execução `RouteView` , o componente:</span><span class="sxs-lookup"><span data-stu-id="3dd19-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="3dd19-119">Recebe o `RouteData` do `Router` junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="3dd19-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="3dd19-120">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="3dd19-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="3dd19-121">Opcionalmente, você pode especificar `DefaultLayout` um parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="3dd19-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="3dd19-122">Os modelos de mais de um padrão `MainLayout` especificam o componente.</span><span class="sxs-lookup"><span data-stu-id="3dd19-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="3dd19-123">*MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="3dd19-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="3dd19-124">Para obter mais informações sobre layouts, <xref:blazor/layouts>consulte.</span><span class="sxs-lookup"><span data-stu-id="3dd19-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="3dd19-125">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="3dd19-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="3dd19-126">O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:</span><span class="sxs-lookup"><span data-stu-id="3dd19-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="3dd19-127">Para que as URLs sejam resolvidas corretamente, o aplicativo `<base>` deve incluir uma marca em seu arquivo *wwwroot/index.html* (Webassembly de mais de um ou mais) ou *páginas/_Host arquivo. cshtml* (servidor mais incrivelmente) com `href` o caminho`<base href="/">`base do aplicativo especificado no atributo ().</span><span class="sxs-lookup"><span data-stu-id="3dd19-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="3dd19-128">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="3dd19-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="3dd19-129">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="3dd19-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="3dd19-130">O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="3dd19-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="3dd19-131">No arquivo *app. Razor* , defina conteúdo personalizado no parâmetro de `NotFound` modelo do `Router` componente:</span><span class="sxs-lookup"><span data-stu-id="3dd19-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="3dd19-132">O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="3dd19-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="3dd19-133">Para aplicar um layout padrão ao `NotFound` conteúdo, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="3dd19-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="3dd19-134">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="3dd19-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="3dd19-135">Use o `AdditionalAssemblies` parâmetro para especificar assemblies adicionais para o `Router` componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="3dd19-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="3dd19-136">Os assemblies especificados são considerados além do `AppAssembly`assembly especificado.</span><span class="sxs-lookup"><span data-stu-id="3dd19-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="3dd19-137">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="3dd19-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="3dd19-138">O exemplo `AdditionalAssemblies` a seguir resulta no suporte de `Component1`roteamento para:</span><span class="sxs-lookup"><span data-stu-id="3dd19-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="3dd19-139">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="3dd19-139">Route parameters</span></span>

<span data-ttu-id="3dd19-140">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="3dd19-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="3dd19-141">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="3dd19-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="3dd19-142">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="3dd19-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="3dd19-143">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3dd19-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="3dd19-144">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="3dd19-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="3dd19-145">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="3dd19-145">Route constraints</span></span>

<span data-ttu-id="3dd19-146">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="3dd19-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="3dd19-147">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="3dd19-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="3dd19-148">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3dd19-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="3dd19-149">O `Id` segmento é um inteiro (`int`).</span><span class="sxs-lookup"><span data-stu-id="3dd19-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="3dd19-150">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="3dd19-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="3dd19-151">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="3dd19-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="3dd19-152">Constraint</span><span class="sxs-lookup"><span data-stu-id="3dd19-152">Constraint</span></span> | <span data-ttu-id="3dd19-153">Exemplo</span><span class="sxs-lookup"><span data-stu-id="3dd19-153">Example</span></span>           | <span data-ttu-id="3dd19-154">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="3dd19-154">Example Matches</span></span>                                                                  | <span data-ttu-id="3dd19-155">Constante</span><span class="sxs-lookup"><span data-stu-id="3dd19-155">Invariant</span></span><br><span data-ttu-id="3dd19-156">culture</span><span class="sxs-lookup"><span data-stu-id="3dd19-156">culture</span></span><br><span data-ttu-id="3dd19-157">correspondência</span><span class="sxs-lookup"><span data-stu-id="3dd19-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="3dd19-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="3dd19-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="3dd19-159">Não</span><span class="sxs-lookup"><span data-stu-id="3dd19-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="3dd19-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="3dd19-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="3dd19-161">Sim</span><span class="sxs-lookup"><span data-stu-id="3dd19-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="3dd19-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="3dd19-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="3dd19-163">Sim</span><span class="sxs-lookup"><span data-stu-id="3dd19-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="3dd19-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="3dd19-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="3dd19-165">Sim</span><span class="sxs-lookup"><span data-stu-id="3dd19-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="3dd19-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="3dd19-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="3dd19-167">Sim</span><span class="sxs-lookup"><span data-stu-id="3dd19-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="3dd19-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="3dd19-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="3dd19-169">Não</span><span class="sxs-lookup"><span data-stu-id="3dd19-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="3dd19-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="3dd19-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="3dd19-171">Sim</span><span class="sxs-lookup"><span data-stu-id="3dd19-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="3dd19-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="3dd19-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="3dd19-173">Sim</span><span class="sxs-lookup"><span data-stu-id="3dd19-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="3dd19-174">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="3dd19-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="3dd19-175">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="3dd19-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="3dd19-176">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="3dd19-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="3dd19-177">Em aplicativos de servidor mais incrivelmente, a rota padrão em *_Host. cshtml* `/` é`@page "/"`().</span><span class="sxs-lookup"><span data-stu-id="3dd19-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="3dd19-178">Uma URL de solicitação que contém um ponto`.`() não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="3dd19-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="3dd19-179">Um aplicativo mais alto retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="3dd19-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="3dd19-180">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="3dd19-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="3dd19-181">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="3dd19-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="3dd19-182">Sintaxe *catch-all* de asterisco duplo (`**`) para capturar o caminho entre vários limites de pasta sem barras invertidas`/`de codificação ().</span><span class="sxs-lookup"><span data-stu-id="3dd19-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="3dd19-183">`path`nome do parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="3dd19-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="3dd19-184">**Não** há suporte para a sintaxe`*`/`**`de parâmetro *catch-all* () em componentes do Razor (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3dd19-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="3dd19-185">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="3dd19-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="3dd19-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="3dd19-186">NavLink component</span></span>

<span data-ttu-id="3dd19-187">Use um `NavLink` componente no lugar de elementos de hiperlink HTML`<a>`() ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="3dd19-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="3dd19-188">Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="3dd19-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="3dd19-189">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="3dd19-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="3dd19-190">O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra `NavLink` como usar componentes:</span><span class="sxs-lookup"><span data-stu-id="3dd19-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="3dd19-191">Há duas `NavLinkMatch` opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="3dd19-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="3dd19-192">`NavLinkMatch.All`&ndash; O `NavLink` está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="3dd19-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="3dd19-193">`NavLinkMatch.Prefix`(*padrão*) &ndash; O `NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="3dd19-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="3dd19-194">No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="3dd19-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="3dd19-195">O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="3dd19-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="3dd19-196">Atributos `NavLink` de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="3dd19-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="3dd19-197">No exemplo a seguir, o `NavLink` componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="3dd19-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="3dd19-198">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="3dd19-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="3dd19-199">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="3dd19-199">URI and navigation state helpers</span></span>

<span data-ttu-id="3dd19-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="3dd19-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="3dd19-201">`NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="3dd19-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="3dd19-202">Membro</span><span class="sxs-lookup"><span data-stu-id="3dd19-202">Member</span></span> | <span data-ttu-id="3dd19-203">Descrição</span><span class="sxs-lookup"><span data-stu-id="3dd19-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="3dd19-204">Uri</span><span class="sxs-lookup"><span data-stu-id="3dd19-204">Uri</span></span> | <span data-ttu-id="3dd19-205">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="3dd19-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="3dd19-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="3dd19-206">BaseUri</span></span> | <span data-ttu-id="3dd19-207">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="3dd19-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="3dd19-208">Normalmente, `BaseUri` corresponde ao `href` atributo no elemento do `<base>` documento em *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (Blazor servidor).</span><span class="sxs-lookup"><span data-stu-id="3dd19-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="3dd19-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="3dd19-209">NavigateTo</span></span> | <span data-ttu-id="3dd19-210">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="3dd19-210">Navigates to the specified URI.</span></span> <span data-ttu-id="3dd19-211">Se `forceLoad` for `true`:</span><span class="sxs-lookup"><span data-stu-id="3dd19-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="3dd19-212">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="3dd19-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="3dd19-213">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="3dd19-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="3dd19-214">Localchanged</span><span class="sxs-lookup"><span data-stu-id="3dd19-214">LocationChanged</span></span> | <span data-ttu-id="3dd19-215">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="3dd19-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="3dd19-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="3dd19-216">ToAbsoluteUri</span></span> | <span data-ttu-id="3dd19-217">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="3dd19-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="3dd19-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="3dd19-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="3dd19-219">Dado um URI de base (por exemplo, um URI retornado anteriormente `GetBaseUri`pelo), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="3dd19-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="3dd19-220">O componente a seguir navega para o componente do `Counter` aplicativo quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="3dd19-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="3dd19-221">O componente a seguir manipula um evento de local alterado.</span><span class="sxs-lookup"><span data-stu-id="3dd19-221">The following component handles a location changed event.</span></span> <span data-ttu-id="3dd19-222">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="3dd19-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="3dd19-223">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="3dd19-223">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="3dd19-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornece as seguintes informações sobre o evento:</span><span class="sxs-lookup"><span data-stu-id="3dd19-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="3dd19-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="3dd19-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="3dd19-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Se `true`, Blazor interceptar a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="3dd19-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="3dd19-227">Se `false`, [navigationmanager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="3dd19-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="3dd19-228">Para obter mais informações sobre a disposição de <xref:blazor/lifecycle#component-disposal-with-idisposable>componentes, consulte.</span><span class="sxs-lookup"><span data-stu-id="3dd19-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
