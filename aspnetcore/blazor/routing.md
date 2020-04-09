---
title: roteamento do núcleo Blazor ASP.NET
author: guardrex
description: Saiba como encaminhar solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218889"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="43c83-103">ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="43c83-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="43c83-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43c83-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="43c83-105">Saiba como encaminhar solicitações e `NavLink` como usar o componente para criar links de navegação em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="43c83-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="43c83-106">ASP.NET integração de roteamento do ponto final do Core</span><span class="sxs-lookup"><span data-stu-id="43c83-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="43c83-107">O Blazor Server é integrado [ao ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="43c83-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="43c83-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões `Startup.Configure`recebidas para componentes interativos com: `MapBlazorHub`</span><span class="sxs-lookup"><span data-stu-id="43c83-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="43c83-109">A configuração mais típica é encaminhar todas as solicitações para uma página de Navalha, que funciona como o host para a parte do lado do servidor do aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="43c83-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="43c83-110">Por convenção, a página *de host* geralmente é nomeada *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="43c83-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="43c83-111">A rota especificada no arquivo host é chamada de *rota de recuo* porque opera com uma baixa prioridade na correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="43c83-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="43c83-112">A rota de recuo é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="43c83-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="43c83-113">Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="43c83-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="43c83-114">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="43c83-114">Route templates</span></span>

<span data-ttu-id="43c83-115">O `Router` componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="43c83-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="43c83-116">O `Router` componente aparece no arquivo *App.razor:*</span><span class="sxs-lookup"><span data-stu-id="43c83-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="43c83-117">Quando um arquivo *.razor* com uma `@page` diretiva é compilado, <xref:Microsoft.AspNetCore.Components.RouteAttribute> a classe gerada é fornecida especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="43c83-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="43c83-118">No tempo de `RouteView` execução, o componente:</span><span class="sxs-lookup"><span data-stu-id="43c83-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="43c83-119">Recebe `RouteData` o `Router` do junto com quaisquer parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="43c83-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="43c83-120">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="43c83-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="43c83-121">Você pode especificar `DefaultLayout` opcionalmente um parâmetro com uma classe de layout para usar para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="43c83-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="43c83-122">Os modelos padrão do `MainLayout` Blazor especificam o componente.</span><span class="sxs-lookup"><span data-stu-id="43c83-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="43c83-123">*MainLayout.razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="43c83-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="43c83-124">Para obter mais informações <xref:blazor/layouts>sobre layouts, consulte .</span><span class="sxs-lookup"><span data-stu-id="43c83-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="43c83-125">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="43c83-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="43c83-126">O componente a seguir `/BlazorRoute` responde `/DifferentBlazorRoute`a solicitações e:</span><span class="sxs-lookup"><span data-stu-id="43c83-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="43c83-127">Para que os URLs resolvam corretamente, o aplicativo deve incluir `<base>` uma tag em seu arquivo *wwwroot/index.html* (Blazor WebAssembly) ou *pages/_Host.cshtml* file (Blazor Server) com o caminho base do aplicativo especificado no atributo `href` ().`<base href="/">`</span><span class="sxs-lookup"><span data-stu-id="43c83-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="43c83-128">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="43c83-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="43c83-129">Forneça conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="43c83-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="43c83-130">O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="43c83-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="43c83-131">No arquivo *App.razor,* defina `NotFound` o conteúdo personalizado `Router` no parâmetro modelo do componente:</span><span class="sxs-lookup"><span data-stu-id="43c83-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="43c83-132">O conteúdo `<NotFound>` das tags pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="43c83-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="43c83-133">Para aplicar um `NotFound` layout padrão <xref:blazor/layouts>ao conteúdo, consulte .</span><span class="sxs-lookup"><span data-stu-id="43c83-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="43c83-134">Rota para componentes de vários conjuntos</span><span class="sxs-lookup"><span data-stu-id="43c83-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="43c83-135">Use `AdditionalAssemblies` o parâmetro para especificar `Router` conjuntos adicionais para o componente a considerar ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="43c83-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="43c83-136">As assembléias especificadas são `AppAssembly`consideradas além do conjunto especificado.</span><span class="sxs-lookup"><span data-stu-id="43c83-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="43c83-137">No exemplo a `Component1` seguir, está um componente routable definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="43c83-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="43c83-138">O `AdditionalAssemblies` exemplo a seguir resulta no suporte de roteamento para: `Component1`</span><span class="sxs-lookup"><span data-stu-id="43c83-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="43c83-139">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="43c83-139">Route parameters</span></span>

<span data-ttu-id="43c83-140">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes com o mesmo nome (caso insensível):</span><span class="sxs-lookup"><span data-stu-id="43c83-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="43c83-141">Os parâmetros opcionais não são suportados.</span><span class="sxs-lookup"><span data-stu-id="43c83-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="43c83-142">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="43c83-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="43c83-143">O primeiro permite a navegação ao componente sem parâmetro.</span><span class="sxs-lookup"><span data-stu-id="43c83-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="43c83-144">A `@page` segunda diretiva `{text}` pega o parâmetro de rota `Text` e atribui o valor à propriedade.</span><span class="sxs-lookup"><span data-stu-id="43c83-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="43c83-145">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="43c83-145">Route constraints</span></span>

<span data-ttu-id="43c83-146">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota a um componente.</span><span class="sxs-lookup"><span data-stu-id="43c83-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="43c83-147">No exemplo a seguir, `Users` a rota para o componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="43c83-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="43c83-148">Um `Id` segmento de rota está presente na URL de solicitação.</span><span class="sxs-lookup"><span data-stu-id="43c83-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="43c83-149">O `Id` segmento é um`int`inteiro ( ).</span><span class="sxs-lookup"><span data-stu-id="43c83-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="43c83-150">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="43c83-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="43c83-151">Para obter as restrições de rota que correspondem à cultura invariante, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="43c83-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="43c83-152">Constraint</span><span class="sxs-lookup"><span data-stu-id="43c83-152">Constraint</span></span> | <span data-ttu-id="43c83-153">Exemplo</span><span class="sxs-lookup"><span data-stu-id="43c83-153">Example</span></span>           | <span data-ttu-id="43c83-154">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="43c83-154">Example Matches</span></span>                                                                  | <span data-ttu-id="43c83-155">Invariável</span><span class="sxs-lookup"><span data-stu-id="43c83-155">Invariant</span></span><br><span data-ttu-id="43c83-156">culture</span><span class="sxs-lookup"><span data-stu-id="43c83-156">culture</span></span><br><span data-ttu-id="43c83-157">correspondência</span><span class="sxs-lookup"><span data-stu-id="43c83-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="43c83-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="43c83-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="43c83-159">Não</span><span class="sxs-lookup"><span data-stu-id="43c83-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="43c83-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="43c83-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="43c83-161">Sim</span><span class="sxs-lookup"><span data-stu-id="43c83-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="43c83-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="43c83-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="43c83-163">Sim</span><span class="sxs-lookup"><span data-stu-id="43c83-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="43c83-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="43c83-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="43c83-165">Sim</span><span class="sxs-lookup"><span data-stu-id="43c83-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="43c83-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="43c83-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="43c83-167">Sim</span><span class="sxs-lookup"><span data-stu-id="43c83-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="43c83-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="43c83-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="43c83-169">Não</span><span class="sxs-lookup"><span data-stu-id="43c83-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="43c83-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="43c83-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="43c83-171">Sim</span><span class="sxs-lookup"><span data-stu-id="43c83-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="43c83-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="43c83-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="43c83-173">Sim</span><span class="sxs-lookup"><span data-stu-id="43c83-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="43c83-174">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="43c83-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="43c83-175">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="43c83-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="43c83-176">Roteamento com URLs que contêm pontilhados</span><span class="sxs-lookup"><span data-stu-id="43c83-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="43c83-177">Nos aplicativos Blazor Server, a rota padrão `/` em`@page "/"` *_Host.cshtml* é ( ).</span><span class="sxs-lookup"><span data-stu-id="43c83-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="43c83-178">Uma URL de solicitação que`.`contém um dot ( ) não é compatível com a rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="43c83-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="43c83-179">Um aplicativo Blazor retorna uma resposta *404 - Não Encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="43c83-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="43c83-180">Para usar rotas que contenham um dot, configure *_Host.cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="43c83-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="43c83-181">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="43c83-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="43c83-182">Sintaxe *de captura de* duplo`**`asterisco () para capturar o caminho através`/`de vários limites de pastas sem codificar barras para frente ().</span><span class="sxs-lookup"><span data-stu-id="43c83-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="43c83-183">`path`nome do parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="43c83-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="43c83-184">*A* sintaxe de`*`/`**`parâmetros de captura () **não** é suportada em componentes de navalha *(.razor*).</span><span class="sxs-lookup"><span data-stu-id="43c83-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="43c83-185">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="43c83-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="43c83-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="43c83-186">NavLink component</span></span>

<span data-ttu-id="43c83-187">Use `NavLink` um componente no lugar de`<a>`elementos de hiperlink HTML ( ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="43c83-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="43c83-188">Um `NavLink` componente se comporta `<a>` como um elemento, `active` exceto que alterna uma `href` classe CSS com base em se corresponde à URL atual.</span><span class="sxs-lookup"><span data-stu-id="43c83-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="43c83-189">A `active` classe ajuda o usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="43c83-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="43c83-190">O `NavMenu` componente a seguir cria uma barra `NavLink` de navegação [Bootstrap](https://getbootstrap.com/docs/) que demonstra como usar componentes:</span><span class="sxs-lookup"><span data-stu-id="43c83-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="43c83-191">Existem `NavLinkMatch` duas opções que você `Match` pode `<NavLink>` atribuir ao atributo do elemento:</span><span class="sxs-lookup"><span data-stu-id="43c83-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="43c83-192">`NavLinkMatch.All`&ndash; O `NavLink` está ativo quando corresponde a toda a URL atual.</span><span class="sxs-lookup"><span data-stu-id="43c83-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="43c83-193">`NavLinkMatch.Prefix`*(padrão)* &ndash; O `NavLink` está ativo quando corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="43c83-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="43c83-194">No exemplo anterior, `NavLink` `href=""` o Home corresponde à `active` URL inicial e só recebe a classe CSS `https://localhost:5001/`na URL de caminho base padrão do aplicativo (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="43c83-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="43c83-195">O `NavLink` segundo `active` recebe a classe quando o `MyComponent` usuário visita `https://localhost:5001/MyComponent` qualquer `https://localhost:5001/MyComponent/AnotherSegment`URL com um prefixo (por exemplo, e ).</span><span class="sxs-lookup"><span data-stu-id="43c83-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="43c83-196">Atributos adicionais `NavLink` do componente são passados para a tag de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="43c83-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="43c83-197">No exemplo a `NavLink` seguir, o `target` componente inclui o atributo:</span><span class="sxs-lookup"><span data-stu-id="43c83-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="43c83-198">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="43c83-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="43c83-199">Ajudadores uri e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="43c83-199">URI and navigation state helpers</span></span>

<span data-ttu-id="43c83-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="43c83-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="43c83-201">`NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="43c83-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="43c83-202">Membro</span><span class="sxs-lookup"><span data-stu-id="43c83-202">Member</span></span> | <span data-ttu-id="43c83-203">Descrição</span><span class="sxs-lookup"><span data-stu-id="43c83-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="43c83-204">Uri</span><span class="sxs-lookup"><span data-stu-id="43c83-204">Uri</span></span> | <span data-ttu-id="43c83-205">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="43c83-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="43c83-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="43c83-206">BaseUri</span></span> | <span data-ttu-id="43c83-207">Obtém o URI base (com uma barra de arrasto) que pode ser preparado para caminhos uri relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="43c83-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="43c83-208">Normalmente, `BaseUri` corresponde ao `href` atributo no `<base>` elemento doBlazor documento em *wwwroot/index.html* ( WebAssembly)Blazor ou *Pages/_Host.cshtml* (Server).</span><span class="sxs-lookup"><span data-stu-id="43c83-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="43c83-209">Navigateto</span><span class="sxs-lookup"><span data-stu-id="43c83-209">NavigateTo</span></span> | <span data-ttu-id="43c83-210">Navega até o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="43c83-210">Navigates to the specified URI.</span></span> <span data-ttu-id="43c83-211">Se `forceLoad` `true`for:</span><span class="sxs-lookup"><span data-stu-id="43c83-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="43c83-212">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="43c83-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="43c83-213">O navegador é forçado a carregar a nova página do servidor, quer o URI seja normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="43c83-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="43c83-214">Locationchanged</span><span class="sxs-lookup"><span data-stu-id="43c83-214">LocationChanged</span></span> | <span data-ttu-id="43c83-215">Um evento que é acionado quando o local de navegação foi alterado.</span><span class="sxs-lookup"><span data-stu-id="43c83-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="43c83-216">Toabsoluteuri</span><span class="sxs-lookup"><span data-stu-id="43c83-216">ToAbsoluteUri</span></span> | <span data-ttu-id="43c83-217">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="43c83-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="43c83-218"><span style="word-break:normal;word-wrap:normal">TobaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="43c83-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="43c83-219">Dado um URI base (por exemplo, `GetBaseUri`um URI anteriormente devolvido), converte um URI absoluto em um URI em relação ao prefixo URI base.</span><span class="sxs-lookup"><span data-stu-id="43c83-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="43c83-220">O seguinte componente navega até `Counter` o componente do aplicativo quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="43c83-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="43c83-221">O componente a seguir lida com um evento alterado de local.</span><span class="sxs-lookup"><span data-stu-id="43c83-221">The following component handles a location changed event.</span></span> <span data-ttu-id="43c83-222">O `HandleLocationChanged` método é desenganado quando `Dispose` é chamado pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="43c83-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="43c83-223">Desengajá-lo permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="43c83-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="43c83-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fornece as seguintes informações sobre o evento:</span><span class="sxs-lookup"><span data-stu-id="43c83-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="43c83-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="43c83-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="43c83-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; `true`Se, Blazor interceptou a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="43c83-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="43c83-227">Se `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) causou a navegação.</span><span class="sxs-lookup"><span data-stu-id="43c83-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="43c83-228">Para obter mais informações <xref:blazor/lifecycle#component-disposal-with-idisposable>sobre o descarte de componentes, consulte .</span><span class="sxs-lookup"><span data-stu-id="43c83-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
