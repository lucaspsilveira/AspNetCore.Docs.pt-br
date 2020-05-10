---
title: Criar e usar componentes Razor de ASP.NET Core
author: guardrex
description: Saiba como criar e usar Razor componentes, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: de8696488292dfc409c5ed83ea0b88a54a001696
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003066"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="37f26-103">Criar e usar componentes Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37f26-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="37f26-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="37f26-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="37f26-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37f26-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="37f26-106">os aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="37f26-106"> apps are built using *components*.</span></span> <span data-ttu-id="37f26-107">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="37f26-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="37f26-108">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="37f26-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="37f26-109">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="37f26-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="37f26-110">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="37f26-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="37f26-111">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="37f26-111">Component classes</span></span>

<span data-ttu-id="37f26-112">Os componentes são implementados em [Razor](xref:mvc/views/razor) arquivos de componente (*. Razor*) usando uma combinação de marcação em C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="37f26-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="37f26-113">Um componente no Blazor é conhecido formalmente como um \* Razor componente\*.</span><span class="sxs-lookup"><span data-stu-id="37f26-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="37f26-114">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="37f26-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="37f26-115">Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="37f26-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="37f26-116">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="37f26-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="37f26-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe C# [Razor](xref:mvc/views/razor)inserida chamada.</span><span class="sxs-lookup"><span data-stu-id="37f26-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="37f26-118">Quando um aplicativo é compilado, a marcação HTML e a lógica de renderização C# são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="37f26-119">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="37f26-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="37f26-120">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="37f26-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="37f26-121">No `@code` bloco, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="37f26-122">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="37f26-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="37f26-123">Os membros do componente podem ser usados como parte da lógica de renderização do componente usando expressões C# que `@`começam com.</span><span class="sxs-lookup"><span data-stu-id="37f26-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="37f26-124">Por exemplo, um campo C# é renderizado pela prefixação `@` para o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="37f26-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="37f26-125">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="37f26-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="37f26-126">`headingFontStyle`para o valor da propriedade de `font-style`CSS para.</span><span class="sxs-lookup"><span data-stu-id="37f26-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="37f26-127">`headingText`para o conteúdo do `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="37f26-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="37f26-128">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="37f26-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="37f26-129">em seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="37f26-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="37f26-130">Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="37f26-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="37f26-131">Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="37f26-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="37f26-132">Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="37f26-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="37f26-133">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37f26-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="37f26-134">Se o namespace raiz do aplicativo for `BlazorApp` e o `Counter` componente residir na pasta *páginas* :</span><span class="sxs-lookup"><span data-stu-id="37f26-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="37f26-135">O `Counter` namespace do componente é `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="37f26-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="37f26-136">O nome do tipo totalmente qualificado do componente é `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="37f26-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="37f26-137">Para obter mais informações, consulte a seção [importar componentes](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="37f26-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="37f26-138">Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37f26-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="37f26-139">Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do `BlazorApp`aplicativo é:</span><span class="sxs-lookup"><span data-stu-id="37f26-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="37f26-140">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="37f26-140">Static assets</span></span>

Blazor<span data-ttu-id="37f26-141">segue a Convenção de ASP.NET Core aplicativos que posicionam ativos estáticos na [pasta raiz da Web do projeto (wwwroot)](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="37f26-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="37f26-142">Use um caminho relativo de base (`/`) para se referir à raiz da Web para um ativo estático.</span><span class="sxs-lookup"><span data-stu-id="37f26-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="37f26-143">No exemplo a seguir, *logo. png* está localizado fisicamente na pasta *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="37f26-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="37f26-144">os componentes **não** dão suporte a notação de`~/`barra de til ().</span><span class="sxs-lookup"><span data-stu-id="37f26-144"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="37f26-145">Para obter informações sobre como definir o caminho base de um <xref:host-and-deploy/blazor/index#app-base-path>aplicativo, consulte.</span><span class="sxs-lookup"><span data-stu-id="37f26-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="37f26-146">Não há suporte para auxiliares de marcas nos componentes</span><span class="sxs-lookup"><span data-stu-id="37f26-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="37f26-147">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) não Razor têm suporte em componentes (arquivos *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="37f26-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="37f26-148">Para fornecer a funcionalidade do tipo auxiliar de Blazormarca no, crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.</span><span class="sxs-lookup"><span data-stu-id="37f26-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="37f26-149">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="37f26-149">Use components</span></span>

<span data-ttu-id="37f26-150">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="37f26-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="37f26-151">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="37f26-152">A marcação a seguir no *index. Razor* renderiza uma `HeadingComponent` instância:</span><span class="sxs-lookup"><span data-stu-id="37f26-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="37f26-153">*Componentes/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="37f26-154">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="37f26-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="37f26-155">A adição `@using` de uma diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.</span><span class="sxs-lookup"><span data-stu-id="37f26-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="37f26-156">Roteamento</span><span class="sxs-lookup"><span data-stu-id="37f26-156">Routing</span></span>

<span data-ttu-id="37f26-157">O roteamento Blazor no é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37f26-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="37f26-158">Quando um Razor arquivo com uma `@page` diretiva é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="37f26-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="37f26-159">Em tempo de execução, o roteador procura classes de componentes `RouteAttribute` com um e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="37f26-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="37f26-160">Para obter mais informações, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="37f26-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="37f26-161">Parâmetros</span><span class="sxs-lookup"><span data-stu-id="37f26-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="37f26-162">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="37f26-162">Route parameters</span></span>

<span data-ttu-id="37f26-163">Os componentes podem receber parâmetros de rota do modelo de rota fornecido `@page` na diretiva.</span><span class="sxs-lookup"><span data-stu-id="37f26-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="37f26-164">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="37f26-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="37f26-165">*Páginas/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="37f26-166">Não há suporte para parâmetros opcionais `@page` , portanto, duas diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="37f26-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="37f26-167">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="37f26-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="37f26-168">A segunda `@page` diretiva recebe o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="37f26-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="37f26-169">A sintaxe de parâmetro *catch-all* (`*`/`**`), que captura o caminho entre vários limites de pasta, **não** tem Razor suporte em componentes (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="37f26-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="37f26-170">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="37f26-170">Component parameters</span></span>

<span data-ttu-id="37f26-171">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente `[Parameter]` com o atributo.</span><span class="sxs-lookup"><span data-stu-id="37f26-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="37f26-172">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="37f26-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="37f26-173">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="37f26-174">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da `Title` Propriedade do. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="37f26-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="37f26-175">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="37f26-176">Não crie componentes que gravam em seus próprios *parâmetros de componente*, use um campo particular em vez disso.</span><span class="sxs-lookup"><span data-stu-id="37f26-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="37f26-177">Para obter mais informações, consulte a seção [não criar componentes que gravam em suas próprias propriedades de parâmetro](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="37f26-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="37f26-178">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="37f26-178">Child content</span></span>

<span data-ttu-id="37f26-179">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-179">Components can set the content of another component.</span></span> <span data-ttu-id="37f26-180">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="37f26-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="37f26-181">No exemplo a seguir, o `ChildComponent` tem uma `ChildContent` propriedade que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="37f26-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="37f26-182">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="37f26-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="37f26-183">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do painel de inicialização `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="37f26-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="37f26-184">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="37f26-185">A propriedade que recebe `RenderFragment` o conteúdo deve ser `ChildContent` nomeada por convenção.</span><span class="sxs-lookup"><span data-stu-id="37f26-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="37f26-186">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar `ChildComponent` o colocando o conteúdo dentro das `<ChildComponent>` marcas.</span><span class="sxs-lookup"><span data-stu-id="37f26-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="37f26-187">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="37f26-188">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="37f26-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="37f26-189">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="37f26-190">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente [`@attributes`](xref:mvc/views/razor#attributes) Razor é renderizado usando a diretiva.</span><span class="sxs-lookup"><span data-stu-id="37f26-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="37f26-191">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="37f26-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="37f26-192">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que dê suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="37f26-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="37f26-193">No exemplo a seguir, o primeiro `<input>` elemento (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o `<input>` segundo elemento`id="useAttributesDict"`() usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="37f26-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="37f26-194">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="37f26-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="37f26-195">Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="37f26-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="37f26-196">Os elementos `<input>` renderizados usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="37f26-196">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="37f26-197">Para aceitar atributos arbitrários, defina um parâmetro de componente `[Parameter]` usando o atributo `CaptureUnmatchedValues` com a propriedade `true`definida como:</span><span class="sxs-lookup"><span data-stu-id="37f26-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="37f26-198">A `CaptureUnmatchedValues` Propriedade on `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="37f26-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="37f26-199">Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="37f26-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="37f26-200">O tipo de propriedade usado `CaptureUnmatchedValues` com deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="37f26-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="37f26-201">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="37f26-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="37f26-202">A posição de `@attributes` relativo à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="37f26-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="37f26-203">Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="37f26-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="37f26-204">Considere o exemplo a seguir de um componente que consome um `Child` componente:</span><span class="sxs-lookup"><span data-stu-id="37f26-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="37f26-205">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="37f26-206">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="37f26-207">O `Child` atributo do `extra` componente é definido à direita de `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="37f26-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="37f26-208">O `Parent` renderizado `<div>` do componente `extra="5"` contém quando passado pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="37f26-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="37f26-209">No exemplo a seguir, a ordem de `extra` e `@attributes` é invertida no `Child` `<div>`:</span><span class="sxs-lookup"><span data-stu-id="37f26-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="37f26-210">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="37f26-211">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="37f26-212">O renderizado `<div>` no `Parent` componente contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="37f26-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="37f26-213">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="37f26-213">Capture references to components</span></span>

<span data-ttu-id="37f26-214">As `Show` referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa `Reset`instância, como ou.</span><span class="sxs-lookup"><span data-stu-id="37f26-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="37f26-215">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="37f26-215">To capture a component reference:</span></span>

* <span data-ttu-id="37f26-216">Adicione um [`@ref`](xref:mvc/views/razor#ref) atributo ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="37f26-217">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="37f26-218">Quando o componente é renderizado, `loginDialog` o campo é populado com a instância de `MyLoginDialog` componente filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-218">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="37f26-219">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="37f26-220">A `loginDialog` variável é populada apenas depois que o componente é renderizado e `MyLoginDialog` sua saída inclui o elemento.</span><span class="sxs-lookup"><span data-stu-id="37f26-220">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="37f26-221">Até esse ponto, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="37f26-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="37f26-222">Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="37f26-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="37f26-223">Para fazer referência a componentes em um loop, consulte [capturar referências para vários componentes filho semelhantes (dotNet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="37f26-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="37f26-224">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), ela não é um recurso de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f26-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="37f26-225">As referências de componente não são passadas para o código&mdash;JavaScript que são usadas apenas no código .net.</span><span class="sxs-lookup"><span data-stu-id="37f26-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="37f26-226">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="37f26-227">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="37f26-228">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="37f26-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="37f26-229">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="37f26-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="37f26-230">usa um `SynchronizationContext` para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="37f26-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="37f26-231">Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de Blazor chamada de evento que `SynchronizationContext`são gerados por são executados nesse.</span><span class="sxs-lookup"><span data-stu-id="37f26-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="37f26-232">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use `InvokeAsync` o método, que será Blazorexpedido `SynchronizationContext`para o.</span><span class="sxs-lookup"><span data-stu-id="37f26-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="37f26-233">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="37f26-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="37f26-234">Registre o `NotifierService` como um singletion:</span><span class="sxs-lookup"><span data-stu-id="37f26-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="37f26-235">No Blazor Webassembly, registre o serviço em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="37f26-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="37f26-236">Em Blazor servidor, registre o serviço em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="37f26-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="37f26-237">Use o `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="37f26-237">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="37f26-238">No exemplo anterior `NotifierService` , invoca o método do `OnNotify` componente fora do. Blazor `SynchronizationContext`</span><span class="sxs-lookup"><span data-stu-id="37f26-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="37f26-239">`InvokeAsync`é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="37f26-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="37f26-240">Use \@a chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="37f26-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="37f26-241">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente Blazormudam, o algoritmo de diferenciação do deve decidir quais elementos ou componentes anteriores podem ser retidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="37f26-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="37f26-242">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="37f26-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="37f26-243">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="37f26-243">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="37f26-244">O conteúdo da `People` coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="37f26-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="37f26-245">Quando o componente é rerenderizado, `<DetailsEditor>` o componente pode ser alterado para `Details` receber valores de parâmetro diferentes.</span><span class="sxs-lookup"><span data-stu-id="37f26-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="37f26-246">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="37f26-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="37f26-247">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="37f26-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="37f26-248">O processo de mapeamento pode ser controlado com [`@key`](xref:mvc/views/razor#key) o atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="37f26-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="37f26-249">`@key`faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="37f26-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="37f26-250">Quando a `People` coleção é alterada, o algoritmo diff mantém a associação entre `<DetailsEditor>` instâncias e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="37f26-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="37f26-251">Se um `Person` for excluído da `People` lista, somente a instância correspondente `<DetailsEditor>` será removida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="37f26-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="37f26-252">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="37f26-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="37f26-253">Se um `Person` for inserido em alguma posição na lista, uma nova `<DetailsEditor>` instância será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="37f26-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="37f26-254">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="37f26-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="37f26-255">Se `Person` as entradas forem reordenadas, as `<DetailsEditor>` instâncias correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="37f26-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="37f26-256">Em alguns cenários, o uso `@key` de minimiza a complexidade da rerenderização e evita possíveis problemas com partes com estado da alteração do dom, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="37f26-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="37f26-257">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="37f26-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="37f26-258">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="37f26-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="37f26-259">Quando usar \@a chave</span><span class="sxs-lookup"><span data-stu-id="37f26-259">When to use \@key</span></span>

<span data-ttu-id="37f26-260">Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um `@foreach` bloco) e um valor adequado existe para definir o `@key`.</span><span class="sxs-lookup"><span data-stu-id="37f26-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="37f26-261">Você também pode usar `@key` para evitar Blazor a preservação de uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="37f26-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="37f26-262">Se `@currentPerson` forem alteradas `@key` , a diretiva Blazor de atributo forçará `<div>` a descartar todo e seus descendentes e RECRIARÁ a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="37f26-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="37f26-263">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja `@currentPerson` preservado quando houver alterações.</span><span class="sxs-lookup"><span data-stu-id="37f26-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="37f26-264">Quando não usar \@a chave</span><span class="sxs-lookup"><span data-stu-id="37f26-264">When not to use \@key</span></span>

<span data-ttu-id="37f26-265">Há um custo de desempenho ao comparar com `@key`.</span><span class="sxs-lookup"><span data-stu-id="37f26-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="37f26-266">O custo de desempenho não é grande, mas `@key` só especifica se controlar as regras de preservação de elementos ou componentes beneficiam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="37f26-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="37f26-267">Mesmo que `@key` não seja usado Blazor , o preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="37f26-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="37f26-268">A única vantagem de usar `@key` o é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="37f26-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="37f26-269">Quais valores usar para \@a chave</span><span class="sxs-lookup"><span data-stu-id="37f26-269">What values to use for \@key</span></span>

<span data-ttu-id="37f26-270">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:</span><span class="sxs-lookup"><span data-stu-id="37f26-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="37f26-271">Instâncias de objeto de modelo (por exemplo `Person` , uma instância como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="37f26-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="37f26-272">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="37f26-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="37f26-273">Identificadores exclusivos (por exemplo, valores de chave primária do `int`tipo `string`, ou `Guid`).</span><span class="sxs-lookup"><span data-stu-id="37f26-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="37f26-274">Verifique se os valores usados `@key` para não conflitam.</span><span class="sxs-lookup"><span data-stu-id="37f26-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="37f26-275">Se valores conflitantes forem detectados no mesmo elemento pai, Blazor o lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="37f26-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="37f26-276">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="37f26-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="37f26-277">Não crie componentes que gravam em suas próprias propriedades de parâmetro</span><span class="sxs-lookup"><span data-stu-id="37f26-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="37f26-278">Os parâmetros são substituídos nas seguintes condições:</span><span class="sxs-lookup"><span data-stu-id="37f26-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="37f26-279">O conteúdo de um componente filho é renderizado `RenderFragment`com um.</span><span class="sxs-lookup"><span data-stu-id="37f26-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="37f26-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>é chamado no componente pai.</span><span class="sxs-lookup"><span data-stu-id="37f26-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="37f26-281">Os parâmetros são redefinidos porque o componente pai é <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> rerenderizado quando é chamado e novos valores de parâmetro são fornecidos ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="37f26-282">Considere o seguinte `Expander` componente que:</span><span class="sxs-lookup"><span data-stu-id="37f26-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="37f26-283">Renderiza conteúdo filho.</span><span class="sxs-lookup"><span data-stu-id="37f26-283">Renders child content.</span></span>
* <span data-ttu-id="37f26-284">Alterna a exibição de conteúdo filho com um parâmetro de componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-284">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="37f26-285">O `Expander` componente é adicionado a um componente pai que pode chamar `StateHasChanged`:</span><span class="sxs-lookup"><span data-stu-id="37f26-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="37f26-286">Inicialmente, os `Expander` componentes se comportam `Expanded` de forma independente quando suas propriedades são alternadas.</span><span class="sxs-lookup"><span data-stu-id="37f26-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="37f26-287">Os componentes filho mantêm seus Estados como esperado.</span><span class="sxs-lookup"><span data-stu-id="37f26-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="37f26-288">Quando `StateHasChanged` é chamado no pai, o `Expanded` parâmetro do primeiro componente filho é redefinido de volta para seu valor inicial (`true`).</span><span class="sxs-lookup"><span data-stu-id="37f26-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="37f26-289">O valor `Expander` do `Expanded` segundo componente não é redefinido porque nenhum conteúdo filho é renderizado no segundo componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="37f26-290">Para manter o estado no cenário anterior, use um *campo particular* no `Expander` componente para manter seu estado de alternância.</span><span class="sxs-lookup"><span data-stu-id="37f26-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="37f26-291">O seguinte `Expander` componente:</span><span class="sxs-lookup"><span data-stu-id="37f26-291">The following `Expander` component:</span></span>

* <span data-ttu-id="37f26-292">Aceita o `Expanded` valor do parâmetro de componente do pai.</span><span class="sxs-lookup"><span data-stu-id="37f26-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="37f26-293">Atribui o valor do parâmetro de componente a um *campo privado* (`expanded`) no [evento OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="37f26-293">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="37f26-294">Usa o campo particular para manter seu estado de alternância interno.</span><span class="sxs-lookup"><span data-stu-id="37f26-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="37f26-295">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="37f26-295">Partial class support</span></span>

Razor<span data-ttu-id="37f26-296">os componentes são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="37f26-296"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="37f26-297">os componentes são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="37f26-297"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="37f26-298">O código C# é definido em [`@code`](xref:mvc/views/razor#code) um bloco com marcação e Razor código HTML em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="37f26-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="37f26-299">os modelos definem seus Razor componentes usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="37f26-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="37f26-300">O código C# é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="37f26-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="37f26-301">O exemplo a seguir mostra o `Counter` componente padrão com `@code` um bloco em um aplicativo gerado a Blazor partir de um modelo.</span><span class="sxs-lookup"><span data-stu-id="37f26-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="37f26-302">A marcação HTML Razor , o código e o código C# estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="37f26-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="37f26-303">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-303">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="37f26-304">O `Counter` componente também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="37f26-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="37f26-305">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="37f26-306">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="37f26-306">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="37f26-307">Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="37f26-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="37f26-308">Os namespaces típicos Razor usados pelos componentes incluem:</span><span class="sxs-lookup"><span data-stu-id="37f26-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="37f26-309">Especificar uma classe base</span><span class="sxs-lookup"><span data-stu-id="37f26-309">Specify a base class</span></span>

<span data-ttu-id="37f26-310">A [`@inherits`](xref:mvc/views/razor#inherits) diretiva pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="37f26-311">O exemplo a seguir mostra como um componente pode herdar uma classe `BlazorRocksBase`base,,, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="37f26-312">A classe base deve derivar `ComponentBase`de.</span><span class="sxs-lookup"><span data-stu-id="37f26-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="37f26-313">*Páginas/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="37f26-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="37f26-314">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="37f26-314">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="37f26-315">Especificar um atributo</span><span class="sxs-lookup"><span data-stu-id="37f26-315">Specify an attribute</span></span>

<span data-ttu-id="37f26-316">Os atributos podem ser especificados Razor em componentes com [`@attribute`](xref:mvc/views/razor#attribute) a diretiva.</span><span class="sxs-lookup"><span data-stu-id="37f26-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="37f26-317">O exemplo a seguir aplica `[Authorize]` o atributo à classe de componente:</span><span class="sxs-lookup"><span data-stu-id="37f26-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="37f26-318">Importar componentes</span><span class="sxs-lookup"><span data-stu-id="37f26-318">Import components</span></span>

<span data-ttu-id="37f26-319">O namespace de um componente criado com Razor o é baseado em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="37f26-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="37f26-320">[`@namespace`](xref:mvc/views/razor#namespace)designação na Razor marcação de arquivo (*. Razor*)`@namespace BlazorSample.MyNamespace`().</span><span class="sxs-lookup"><span data-stu-id="37f26-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="37f26-321">O projeto `RootNamespace` no arquivo de projeto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="37f26-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="37f26-322">O nome do projeto, obtido do nome do arquivo do projeto (*. csproj*) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="37f26-323">Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="37f26-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="37f26-324">Os componentes seguem regras de associação de nome C#.</span><span class="sxs-lookup"><span data-stu-id="37f26-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="37f26-325">Para o `Index` componente neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="37f26-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="37f26-326">Na mesma pasta, *páginas*.</span><span class="sxs-lookup"><span data-stu-id="37f26-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="37f26-327">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="37f26-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="37f26-328">Os componentes definidos em um namespace diferente são trazidos para Razoro [`@using`](xref:mvc/views/razor#using) escopo usando a diretiva do.</span><span class="sxs-lookup"><span data-stu-id="37f26-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="37f26-329">Se outro componente, `NavMenu.razor`, existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado no `Index.razor` com a seguinte `@using` instrução:</span><span class="sxs-lookup"><span data-stu-id="37f26-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="37f26-330">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que [`@using`](xref:mvc/views/razor#using) não exige a diretiva:</span><span class="sxs-lookup"><span data-stu-id="37f26-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="37f26-331">Não `global::` há suporte para a qualificação.</span><span class="sxs-lookup"><span data-stu-id="37f26-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="37f26-332">Não há suporte para a `using` importação de componentes com instruções `@using Foo = Bar`com alias (por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="37f26-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="37f26-333">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="37f26-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="37f26-334">Por exemplo, não `@using BlazorSample` há suporte `NavMenu.razor` para `<Shared.NavMenu></Shared.NavMenu>` adicionar e referenciar com.</span><span class="sxs-lookup"><span data-stu-id="37f26-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="37f26-335">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="37f26-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="37f26-336">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="37f26-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="37f26-337">Se o valor for `false` ou `null`, o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="37f26-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="37f26-338">Se o valor é `true`, o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="37f26-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="37f26-339">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="37f26-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="37f26-340">Se `IsCompleted` for `true`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="37f26-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="37f26-341">Se `IsCompleted` for `false`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="37f26-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="37f26-342">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="37f26-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="37f26-343">Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool`.</span><span class="sxs-lookup"><span data-stu-id="37f26-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="37f26-344">Nesses casos, use um `string` tipo em vez de um `bool`.</span><span class="sxs-lookup"><span data-stu-id="37f26-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="37f26-345">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="37f26-345">Raw HTML</span></span>

<span data-ttu-id="37f26-346">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="37f26-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="37f26-347">Para renderizar HTML bruto, empacote o conteúdo HTML em `MarkupString` um valor.</span><span class="sxs-lookup"><span data-stu-id="37f26-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="37f26-348">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="37f26-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="37f26-349">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="37f26-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="37f26-350">O exemplo a seguir mostra como `MarkupString` usar o tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="37f26-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="37f26-351">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="37f26-351">Cascading values and parameters</span></span>

<span data-ttu-id="37f26-352">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="37f26-353">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="37f26-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="37f26-354">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="37f26-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="37f26-355">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="37f26-355">Theme example</span></span>

<span data-ttu-id="37f26-356">No exemplo a seguir do aplicativo de exemplo, a `ThemeInfo` classe especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="37f26-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="37f26-357">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="37f26-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="37f26-358">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="37f26-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="37f26-359">O `CascadingValue` componente encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="37f26-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="37f26-360">Por exemplo, o aplicativo de exemplo especifica informações de`ThemeInfo`tema () em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do `@Body` layout da propriedade.</span><span class="sxs-lookup"><span data-stu-id="37f26-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="37f26-361">`ButtonClass`é atribuído um valor de `btn-success` no componente layout.</span><span class="sxs-lookup"><span data-stu-id="37f26-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="37f26-362">Qualquer componente descendente pode consumir essa propriedade por meio `ThemeInfo` do objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="37f26-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="37f26-363">`CascadingValuesParametersLayout`componente</span><span class="sxs-lookup"><span data-stu-id="37f26-363">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="37f26-364">Para fazer uso de valores em cascata, os componentes declaram parâmetros em `[CascadingParameter]` cascata usando o atributo.</span><span class="sxs-lookup"><span data-stu-id="37f26-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="37f26-365">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="37f26-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="37f26-366">No aplicativo de exemplo, o `CascadingValuesParametersTheme` componente associa o `ThemeInfo` valor em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="37f26-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="37f26-367">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="37f26-368">`CascadingValuesParametersTheme`componente</span><span class="sxs-lookup"><span data-stu-id="37f26-368">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="37f26-369">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia `Name` de caracteres exclusiva `CascadingValue` para cada componente e `CascadingParameter`seu correspondente.</span><span class="sxs-lookup"><span data-stu-id="37f26-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="37f26-370">No exemplo a seguir, dois `CascadingValue` componentes em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="37f26-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="37f26-371">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="37f26-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="37f26-372">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="37f26-372">TabSet example</span></span>

<span data-ttu-id="37f26-373">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="37f26-374">Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="37f26-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="37f26-375">O aplicativo de exemplo tem `ITab` uma interface que implementa as guias:</span><span class="sxs-lookup"><span data-stu-id="37f26-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="37f26-376">O `CascadingValuesParametersTabSet` componente usa o `TabSet` componente, que contém vários `Tab` componentes:</span><span class="sxs-lookup"><span data-stu-id="37f26-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="37f26-377">Os componentes `Tab` filho não são passados explicitamente como parâmetros para `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="37f26-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="37f26-378">Em vez disso, `Tab` os componentes filho fazem parte do conteúdo filho do `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="37f26-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="37f26-379">No entanto `TabSet` , o ainda precisa saber sobre `Tab` cada componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, `TabSet` o componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado `Tab` pelos componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="37f26-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="37f26-380">`TabSet`componente</span><span class="sxs-lookup"><span data-stu-id="37f26-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="37f26-381">Os componentes `Tab` descendentes capturam `TabSet` o que contém como um parâmetro em cascata `Tab` , de modo que os `TabSet` componentes se adicionam à coordenada e na qual a guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="37f26-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="37f26-382">`Tab`componente</span><span class="sxs-lookup"><span data-stu-id="37f26-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="37f26-383">modelo</span><span class="sxs-lookup"><span data-stu-id="37f26-383"> templates</span></span>

<span data-ttu-id="37f26-384">Os fragmentos de renderização podem ser Razor definidos usando a sintaxe do modelo.</span><span class="sxs-lookup"><span data-stu-id="37f26-384">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="37f26-385">os modelos são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="37f26-385"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="37f26-386">O exemplo a seguir ilustra como especificar `RenderFragment` e `RenderFragment<T>` valores e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="37f26-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="37f26-387">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="37f26-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="37f26-388">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="37f26-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="37f26-389">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="37f26-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="37f26-390">Como Blazor o RENDERIZA imagens html, com suporte para navegadores, incluindo imagens SVG (gráficos vetoriais escalonáveis) (*. svg*), `<img>` há suporte por meio da marca:</span><span class="sxs-lookup"><span data-stu-id="37f26-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="37f26-391">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos (*. css*):</span><span class="sxs-lookup"><span data-stu-id="37f26-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="37f26-392">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="37f26-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="37f26-393">Se você posicionar `<svg>` uma marca diretamente em um arquivo de componente (*. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="37f26-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="37f26-394">Por exemplo, `<use>` as marcas não são respeitadas `@bind` atualmente e não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="37f26-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="37f26-395">Esperamos abordar essas limitações em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="37f26-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37f26-396">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="37f26-396">Additional resources</span></span>

* <span data-ttu-id="37f26-397"><xref:security/blazor/server/threat-mitigation>&ndash; Inclui diretrizes sobre a Blazor criação de aplicativos de servidor que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="37f26-397"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
