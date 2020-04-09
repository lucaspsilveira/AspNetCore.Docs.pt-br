---
title: Criar e usar ASP.NET componentes da Navalha Central
author: guardrex
description: Saiba como criar e usar componentes da Razor, incluindo como se vincular a dados, lidar com eventos e gerenciar ciclos de vida dos componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: bc1d07aef9cd60b89343a034168daa6754f4421b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306506"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="4868b-103">Criar e usar ASP.NET componentes da Navalha Central</span><span class="sxs-lookup"><span data-stu-id="4868b-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="4868b-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4868b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4868b-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4868b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="4868b-106">aplicativos são *construídos*usando componentes .</span><span class="sxs-lookup"><span data-stu-id="4868b-106"> apps are built using *components*.</span></span> <span data-ttu-id="4868b-107">Um componente é um pedaço independente da interface de usuário (UI), como uma página, diálogo ou formulário.</span><span class="sxs-lookup"><span data-stu-id="4868b-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="4868b-108">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos de IA.</span><span class="sxs-lookup"><span data-stu-id="4868b-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="4868b-109">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="4868b-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="4868b-110">Eles podem ser aninhados, reutilizados e compartilhados entre os projetos.</span><span class="sxs-lookup"><span data-stu-id="4868b-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="4868b-111">Classes componentes</span><span class="sxs-lookup"><span data-stu-id="4868b-111">Component classes</span></span>

<span data-ttu-id="4868b-112">Os componentes são implementados em arquivos de [componentes Razor](xref:mvc/views/razor) *(.razor)* usando uma combinação de marcação C# e HTML.</span><span class="sxs-lookup"><span data-stu-id="4868b-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="4868b-113">Um componente Blazor em é formalmente referido como um *componente Razor*.</span><span class="sxs-lookup"><span data-stu-id="4868b-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="4868b-114">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="4868b-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="4868b-115">Por exemplo, *MyCoolComponent.razor* é válido e *myCoolComponent.razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="4868b-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="4868b-116">A ida de ida para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="4868b-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="4868b-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="4868b-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="4868b-118">Quando um aplicativo é compilado, a lógica de marcação HTML e C# são convertidas em uma classe de componentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="4868b-119">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4868b-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="4868b-120">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="4868b-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="4868b-121">No `@code` bloco, o estado do componente (propriedades, campos) é especificado com métodos para o manuseio de eventos ou para definir a lógica de outros componentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="4868b-122">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="4868b-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="4868b-123">Os membros do componente podem ser usados como parte da lógica `@`de renderização do componente usando expressões C# que começam com .</span><span class="sxs-lookup"><span data-stu-id="4868b-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="4868b-124">Por exemplo, um campo C# é `@` renderizado prefixando o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="4868b-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="4868b-125">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="4868b-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="4868b-126">`_headingFontStyle`ao valor da propriedade `font-style`CSS para .</span><span class="sxs-lookup"><span data-stu-id="4868b-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="4868b-127">`_headingText`ao conteúdo do `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="4868b-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="4868b-128">Depois que o componente é renderizado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="4868b-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="4868b-129">em seguida, compara a nova árvore de renderização com a anterior e aplica quaisquer modificações no DOM (Document Object Model, modelo de objeto de documento) do navegador.</span><span class="sxs-lookup"><span data-stu-id="4868b-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="4868b-130">Os componentes são classes C# comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="4868b-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="4868b-131">Os componentes que produzem páginas da Web geralmente residem na pasta *Páginas.*</span><span class="sxs-lookup"><span data-stu-id="4868b-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="4868b-132">Os componentes não-página são frequentemente colocados na pasta *Compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="4868b-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="4868b-133">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e da localização (pasta) do componente dentro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4868b-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="4868b-134">Se o namespace raiz `BlazorApp` do `Counter` aplicativo for e o componente residir na pasta *Páginas:*</span><span class="sxs-lookup"><span data-stu-id="4868b-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="4868b-135">O `Counter` espaço de nome `BlazorApp.Pages`do componente é .</span><span class="sxs-lookup"><span data-stu-id="4868b-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="4868b-136">O nome de tipo totalmente `BlazorApp.Pages.Counter`qualificado do componente é .</span><span class="sxs-lookup"><span data-stu-id="4868b-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="4868b-137">Para obter mais informações, consulte a seção [Importar componentes.](#import-components)</span><span class="sxs-lookup"><span data-stu-id="4868b-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="4868b-138">Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports.razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4868b-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="4868b-139">Por exemplo, o namespace a seguir torna os componentes em uma `BlazorApp`pasta *Componentes* disponíveis quando o namespace raiz do aplicativo é :</span><span class="sxs-lookup"><span data-stu-id="4868b-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="4868b-140">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="4868b-140">Static assets</span></span>

Blazor<span data-ttu-id="4868b-141">segue a convenção de aplicativos ASP.NET Core que colocam ativos estáticos sob a [pasta web root (wwwroot)](xref:fundamentals/index#web-root)do projeto .</span><span class="sxs-lookup"><span data-stu-id="4868b-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="4868b-142">Use um caminho relativo à base (`/`) para se referir à raiz da web para um ativo estático.</span><span class="sxs-lookup"><span data-stu-id="4868b-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="4868b-143">No exemplo a seguir, *o logo.png* está fisicamente localizado na pasta *{PROJECT ROOT}/wwwroot/images:*</span><span class="sxs-lookup"><span data-stu-id="4868b-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="4868b-144">Os componentes da navalha **não** suportam notação de corte de tilde ().`~/`</span><span class="sxs-lookup"><span data-stu-id="4868b-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="4868b-145">Para obter informações sobre como definir <xref:host-and-deploy/blazor/index#app-base-path>o caminho base de um aplicativo, consulte .</span><span class="sxs-lookup"><span data-stu-id="4868b-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="4868b-146">Os ajudantes de marcação não são suportados em componentes</span><span class="sxs-lookup"><span data-stu-id="4868b-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="4868b-147">[Os ajudantes de](xref:mvc/views/tag-helpers/intro) marcação não são suportados em componentes razor *(arquivos .razor).*</span><span class="sxs-lookup"><span data-stu-id="4868b-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="4868b-148">Para fornecer a funcionalidade semelhante Blazorao Tag Helper, crie um componente com a mesma funcionalidade do Tag Helper e use o componente em vez disso.</span><span class="sxs-lookup"><span data-stu-id="4868b-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="4868b-149">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="4868b-149">Use components</span></span>

<span data-ttu-id="4868b-150">Os componentes podem incluir outros componentes declarando-os usando sintaxe de elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="4868b-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="4868b-151">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="4868b-152">A marcação a seguir em `HeadingComponent` *Index.razor* torna uma instância:</span><span class="sxs-lookup"><span data-stu-id="4868b-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="4868b-153">*Componentes/HeadingComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="4868b-154">Se um componente contiver um elemento HTML com uma primeira letra maiúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="4868b-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="4868b-155">A `@using` adição de uma diretiva para o namespace do componente torna o componente disponível, o que resolve o aviso.</span><span class="sxs-lookup"><span data-stu-id="4868b-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="4868b-156">Roteamento</span><span class="sxs-lookup"><span data-stu-id="4868b-156">Routing</span></span>

<span data-ttu-id="4868b-157">O roteamento Blazor é alcançado fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4868b-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="4868b-158">Quando um arquivo `@page` Razor com uma diretiva é compilado, a classe gerada recebe uma <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificação do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="4868b-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="4868b-159">Em tempo de execução, o roteador procura classes de componentes com a `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="4868b-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="4868b-160">Para obter mais informações, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="4868b-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="4868b-161">parâmetros</span><span class="sxs-lookup"><span data-stu-id="4868b-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="4868b-162">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="4868b-162">Route parameters</span></span>

<span data-ttu-id="4868b-163">Os componentes podem receber parâmetros de `@page` rota a partir do modelo de rota fornecido na diretiva.</span><span class="sxs-lookup"><span data-stu-id="4868b-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="4868b-164">O roteador usa parâmetros de rota para preencher os parâmetros de componentecorrespondentes correspondentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="4868b-165">*Páginas/RouteParameter.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="4868b-166">Os parâmetros opcionais não são `@page` suportados, portanto, duas diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="4868b-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="4868b-167">O primeiro permite a navegação ao componente sem parâmetro.</span><span class="sxs-lookup"><span data-stu-id="4868b-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="4868b-168">A `@page` segunda diretiva `{text}` recebe o parâmetro de rota `Text` e atribui o valor à propriedade.</span><span class="sxs-lookup"><span data-stu-id="4868b-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="4868b-169">A sintaxe de`*`/`**`parâmetros *catch-all* (), que captura o caminho através de vários limites de pastas, **não** é suportada em componentes razor *(.razor*).</span><span class="sxs-lookup"><span data-stu-id="4868b-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="4868b-170">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="4868b-170">Component parameters</span></span>

<span data-ttu-id="4868b-171">Os componentes podem ter *parâmetros de componentes,* que `[Parameter]` são definidos usando propriedades públicas na classe de componentes com o atributo.</span><span class="sxs-lookup"><span data-stu-id="4868b-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="4868b-172">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="4868b-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="4868b-173">*Componentes/ChildComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="4868b-174">No exemplo a seguir do `ParentComponent` aplicativo de amostra, define o valor da `Title` propriedade do `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="4868b-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="4868b-175">*Páginas/ParentComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="4868b-176">Conteúdo infantil</span><span class="sxs-lookup"><span data-stu-id="4868b-176">Child content</span></span>

<span data-ttu-id="4868b-177">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-177">Components can set the content of another component.</span></span> <span data-ttu-id="4868b-178">O componente atribuído fornece o conteúdo entre as tags que especificam o componente receptor.</span><span class="sxs-lookup"><span data-stu-id="4868b-178">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="4868b-179">No exemplo a `ChildComponent` seguir, `ChildContent` o tem `RenderFragment`uma propriedade que representa um , que representa um segmento de UI para renderizar.</span><span class="sxs-lookup"><span data-stu-id="4868b-179">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="4868b-180">O valor `ChildContent` do é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="4868b-180">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="4868b-181">O valor `ChildContent` do é recebido do componente pai e renderizado dentro do `panel-body`painel Bootstrap .</span><span class="sxs-lookup"><span data-stu-id="4868b-181">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="4868b-182">*Componentes/ChildComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="4868b-183">A propriedade `RenderFragment` que recebe `ChildContent` o conteúdo deve ser nomeada por convenção.</span><span class="sxs-lookup"><span data-stu-id="4868b-183">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="4868b-184">O `ParentComponent` aplicativo na amostra pode fornecer `ChildComponent` conteúdo para renderização, colocando o conteúdo dentro das `<ChildComponent>` tags.</span><span class="sxs-lookup"><span data-stu-id="4868b-184">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="4868b-185">*Páginas/ParentComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-185">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="4868b-186">Splatting de atributos e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="4868b-186">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="4868b-187">Os componentes podem capturar e renderizar atributos adicionais, além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-187">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="4868b-188">Atributos adicionais podem ser capturados em um dicionário e, em seguida, [`@attributes`](xref:mvc/views/razor#attributes) *splatted* em um elemento quando o componente é renderizado usando a diretiva Razor.</span><span class="sxs-lookup"><span data-stu-id="4868b-188">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="4868b-189">Este cenário é útil ao definir um componente que produz um elemento de marcação que suporta uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="4868b-189">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="4868b-190">Por exemplo, pode ser tedioso definir atributos separadamente para um `<input>` que suporta muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="4868b-190">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="4868b-191">No exemplo a seguir, `<input>` `id="useIndividualParams"`o primeiro elemento ( ) `<input>` usa`id="useAttributesDict"`parâmetros de componentes individuais, enquanto o segundo elemento ( ) usa splatting de atributo:</span><span class="sxs-lookup"><span data-stu-id="4868b-191">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="4868b-192">O tipo de parâmetro `IEnumerable<KeyValuePair<string, object>>` deve ser implementado com teclas de string.</span><span class="sxs-lookup"><span data-stu-id="4868b-192">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="4868b-193">Usar `IReadOnlyDictionary<string, object>` também é uma opção neste cenário.</span><span class="sxs-lookup"><span data-stu-id="4868b-193">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="4868b-194">Os elementos renderizados `<input>` que utilizam ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="4868b-194">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="4868b-195">Para aceitar atributos arbitrários, `[Parameter]` defina `CaptureUnmatchedValues` um `true`parâmetro de componente usando o atributo com a propriedade definida como:</span><span class="sxs-lookup"><span data-stu-id="4868b-195">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="4868b-196">A `CaptureUnmatchedValues` propriedade `[Parameter]` em permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="4868b-196">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="4868b-197">Um componente só pode definir `CaptureUnmatchedValues`um parâmetro único com .</span><span class="sxs-lookup"><span data-stu-id="4868b-197">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="4868b-198">O tipo de `CaptureUnmatchedValues` propriedade usado deve `Dictionary<string, object>` ser atribuído a partir de teclas de string.</span><span class="sxs-lookup"><span data-stu-id="4868b-198">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="4868b-199">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="4868b-199">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="4868b-200">A posição `@attributes` em relação à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="4868b-200">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="4868b-201">Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (de última para primeira).</span><span class="sxs-lookup"><span data-stu-id="4868b-201">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="4868b-202">Considere o seguinte exemplo de um `Child` componente que consome um componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-202">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="4868b-203">*ParentComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-203">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="4868b-204">*ChildComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-204">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="4868b-205">O `Child` atributo `extra` do componente está `@attributes`definido à direita de .</span><span class="sxs-lookup"><span data-stu-id="4868b-205">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="4868b-206">O `Parent` componente renderizado `<div>` `extra="5"` contém quando passado através do atributo adicional porque os atributos são processados da direita para a esquerda (da última à primeira):</span><span class="sxs-lookup"><span data-stu-id="4868b-206">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="4868b-207">No exemplo a seguir, `extra` `@attributes` a ordem e `Child` é `<div>`invertida no componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-207">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="4868b-208">*ParentComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-208">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="4868b-209">*ChildComponent.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-209">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="4868b-210">A renderização `<div>` `Parent` no `extra="10"` componente contém quando passada através do atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="4868b-210">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="4868b-211">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="4868b-211">Capture references to components</span></span>

<span data-ttu-id="4868b-212">As referências de componentes fornecem uma maneira de referenciar uma instância `Show` de `Reset`componente para que você possa emitir comandos para essa instância, como ou .</span><span class="sxs-lookup"><span data-stu-id="4868b-212">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="4868b-213">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-213">To capture a component reference:</span></span>

* <span data-ttu-id="4868b-214">Adicione [`@ref`](xref:mvc/views/razor#ref) um atributo ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="4868b-214">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="4868b-215">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="4868b-215">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="4868b-216">Quando o componente é `_loginDialog` renderizado, o `MyLoginDialog` campo é preenchido com a instância do componente filho.</span><span class="sxs-lookup"><span data-stu-id="4868b-216">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="4868b-217">Em seguida, você pode invocar métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-217">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4868b-218">A `_loginDialog` variável só é preenchida após a renderização do `MyLoginDialog` componente e sua saída inclui o elemento.</span><span class="sxs-lookup"><span data-stu-id="4868b-218">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="4868b-219">Até lá, não há nada para se referir.</span><span class="sxs-lookup"><span data-stu-id="4868b-219">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="4868b-220">Para manipular as referências dos componentes após a renderização concluída do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="4868b-220">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="4868b-221">Para referenciar componentes em um loop, consulte [Capturar referências a vários componentes infantis semelhantes (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="4868b-221">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="4868b-222">Embora a captura de referências de componentes use uma sintaxe semelhante à [captura de referências de elementos,](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)não é um recurso de interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4868b-222">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="4868b-223">As referências dos componentes não&mdash;são passadas para o código JavaScript, elas são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="4868b-223">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="4868b-224">**Não utilize** referências de componentes para alterar o estado dos componentes infantis.</span><span class="sxs-lookup"><span data-stu-id="4868b-224">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="4868b-225">Em vez disso, use parâmetros declarativos normais para passar dados para componentes infantis.</span><span class="sxs-lookup"><span data-stu-id="4868b-225">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="4868b-226">O uso de parâmetros declarativos normais resulta em componentes infantis que rerenderizam automaticamente nos horários corretos.</span><span class="sxs-lookup"><span data-stu-id="4868b-226">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="4868b-227">Invoque métodos de componentes externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="4868b-227">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="4868b-228">usa `SynchronizationContext` um para impor um único segmento lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="4868b-228"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="4868b-229">Os [métodos](xref:blazor/lifecycle) de ciclo de vida de um componente Blazor e quaisquer retornos de chamada de evento que são levantados por são executados neste `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="4868b-229">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="4868b-230">No caso de um componente ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o `InvokeAsync` método, que será enviado para Blazoro 's `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="4868b-230">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="4868b-231">Por exemplo, considere um *serviço de notificantes* que possa notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="4868b-231">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="4868b-232">Registre `NotifierService` o como singletion:</span><span class="sxs-lookup"><span data-stu-id="4868b-232">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="4868b-233">No Blazor WebAssembly, registre `Program.Main`o serviço em :</span><span class="sxs-lookup"><span data-stu-id="4868b-233">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="4868b-234">No Blazor Servidor, registre `Startup.ConfigureServices`o serviço em :</span><span class="sxs-lookup"><span data-stu-id="4868b-234">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="4868b-235">Use `NotifierService` o para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-235">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="4868b-236">No exemplo `NotifierService` anterior, invoca o `OnNotify` método do Blazorcomponente `SynchronizationContext`fora de 's .</span><span class="sxs-lookup"><span data-stu-id="4868b-236">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="4868b-237">`InvokeAsync`é usado para mudar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="4868b-237">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="4868b-238">Use \@a chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="4868b-238">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="4868b-239">Ao renderizar uma lista de elementos ou componentes Blazore os elementos ou componentes posteriormente mudarem, o algoritmo de difusão deve decidir quais dos elementos ou componentes anteriores podem ser retidos e como os objetos do modelo devem mapeá-los.</span><span class="sxs-lookup"><span data-stu-id="4868b-239">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="4868b-240">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você pode querer controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="4868b-240">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="4868b-241">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4868b-241">Consider the following example:</span></span>

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

<span data-ttu-id="4868b-242">O conteúdo `People` da coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="4868b-242">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="4868b-243">Quando o componente se `<DetailsEditor>` rerenderiza, o `Details` componente pode mudar para receber diferentes valores de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="4868b-243">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="4868b-244">Isso pode causar uma rerenderização mais complexa do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="4868b-244">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="4868b-245">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco perdido do elemento.</span><span class="sxs-lookup"><span data-stu-id="4868b-245">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="4868b-246">O processo de mapeamento `@key` pode ser controlado com o atributo diretivo.</span><span class="sxs-lookup"><span data-stu-id="4868b-246">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="4868b-247">`@key`faz com que o algoritmo de difusão garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="4868b-247">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="4868b-248">Quando `People` a coleção muda, o algoritmo de `<DetailsEditor>` difusão retém a associação entre instâncias e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="4868b-248">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="4868b-249">Se `Person` um for excluído `People` da lista, `<DetailsEditor>` apenas a instância correspondente será removida da ui.</span><span class="sxs-lookup"><span data-stu-id="4868b-249">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="4868b-250">Outras instâncias são deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="4868b-250">Other instances are left unchanged.</span></span>
* <span data-ttu-id="4868b-251">Se `Person` um for inserido em alguma posição `<DetailsEditor>` na lista, uma nova instância será inserida nessa posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="4868b-251">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="4868b-252">Outras instâncias são deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="4868b-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="4868b-253">Se `Person` as entradas forem reordenadas, as instâncias correspondentes `<DetailsEditor>` serão preservadas e reordenadas na ui.</span><span class="sxs-lookup"><span data-stu-id="4868b-253">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="4868b-254">Em alguns cenários, o uso de `@key` minimiza a complexidade da rerenderização e evita possíveis problemas com partes estaduais do DOM mudando, como a posição de foco.</span><span class="sxs-lookup"><span data-stu-id="4868b-254">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4868b-255">As teclas são locais para cada elemento ou componente do contêiner.</span><span class="sxs-lookup"><span data-stu-id="4868b-255">Keys are local to each container element or component.</span></span> <span data-ttu-id="4868b-256">As chaves não são comparadas globalmente através do documento.</span><span class="sxs-lookup"><span data-stu-id="4868b-256">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="4868b-257">Quando usar \@a chave</span><span class="sxs-lookup"><span data-stu-id="4868b-257">When to use \@key</span></span>

<span data-ttu-id="4868b-258">Normalmente, faz sentido `@key` usar sempre que uma lista é `@foreach` renderizada (por exemplo, em `@key`um bloco) e existe um valor adequado para definir o .</span><span class="sxs-lookup"><span data-stu-id="4868b-258">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="4868b-259">Você também `@key` pode Blazor usar para evitar a preservação de um elemento ou subárvore componente quando um objeto muda:</span><span class="sxs-lookup"><span data-stu-id="4868b-259">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="4868b-260">Se `@currentPerson` mudar, `@key` a Blazor diretiva de `<div>` atributo força a descartar o todo e seus descendentes e reconstruir a subárvore dentro da UI com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-260">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="4868b-261">Isso pode ser útil se você precisar garantir que `@currentPerson` nenhum estado de IU seja preservado quando mudar.</span><span class="sxs-lookup"><span data-stu-id="4868b-261">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="4868b-262">Quando não \@usar a chave</span><span class="sxs-lookup"><span data-stu-id="4868b-262">When not to use \@key</span></span>

<span data-ttu-id="4868b-263">Há um custo de desempenho `@key`quando se diffing com .</span><span class="sxs-lookup"><span data-stu-id="4868b-263">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="4868b-264">O custo de desempenho não é `@key` grande, mas apenas especificar se o controle do elemento ou regras de preservação de componentes beneficiam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4868b-264">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="4868b-265">Mesmo `@key` que não seja Blazor usado, preserva o elemento infantil e as instâncias dos componentes tanto quanto possível.</span><span class="sxs-lookup"><span data-stu-id="4868b-265">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="4868b-266">A única vantagem `@key` a ser usado é o controle sobre *como* as instâncias do modelo são mapeadas para as instâncias de componentes preservados, em vez do algoritmo de difusão que seleciona o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="4868b-266">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="4868b-267">Quais valores \@usar como chave</span><span class="sxs-lookup"><span data-stu-id="4868b-267">What values to use for \@key</span></span>

<span data-ttu-id="4868b-268">Geralmente, faz sentido fornecer um dos seguintes `@key`tipos de valor para:</span><span class="sxs-lookup"><span data-stu-id="4868b-268">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="4868b-269">Modelo de instâncias de `Person` objeto (por exemplo, uma instância como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="4868b-269">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="4868b-270">Isso garante a preservação com base na igualdade de referência de objetos.</span><span class="sxs-lookup"><span data-stu-id="4868b-270">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="4868b-271">Identificadores únicos (por exemplo, valores-chave primários do tipo, `int` `string`ou `Guid`).</span><span class="sxs-lookup"><span data-stu-id="4868b-271">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="4868b-272">Certifique-se de `@key` que os valores usados não se chocam.</span><span class="sxs-lookup"><span data-stu-id="4868b-272">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="4868b-273">Se os valores conflitantes forem Blazor detectados dentro do mesmo elemento pai, lança uma exceção porque não pode mapear deterministicamente elementos antigos ou componentes para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-273">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="4868b-274">Use apenas valores distintos, como instâncias de objeto ou valores-chave primários.</span><span class="sxs-lookup"><span data-stu-id="4868b-274">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="4868b-275">Suporte parcial da classe</span><span class="sxs-lookup"><span data-stu-id="4868b-275">Partial class support</span></span>

<span data-ttu-id="4868b-276">Os componentes da navalha são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="4868b-276">Razor components are generated as partial classes.</span></span> <span data-ttu-id="4868b-277">Os componentes da navalha são de autoria usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4868b-277">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="4868b-278">C# code é [`@code`](xref:mvc/views/razor#code) definido em um bloco com marcação HTML e código Razor em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="4868b-278">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="4868b-279">os modelos definem seus componentes razor usando esta abordagem.</span><span class="sxs-lookup"><span data-stu-id="4868b-279"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="4868b-280">O código C# é colocado em um arquivo de código atrás definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="4868b-280">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="4868b-281">O exemplo a `Counter` seguir mostra `@code` o componente padrão Blazor com um bloco em um aplicativo gerado a partir de um modelo.</span><span class="sxs-lookup"><span data-stu-id="4868b-281">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="4868b-282">Marcação HTML, código de navalha e código C# estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="4868b-282">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="4868b-283">*Contador.navalha:*</span><span class="sxs-lookup"><span data-stu-id="4868b-283">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="4868b-284">O `Counter` componente também pode ser criado usando um arquivo de código atrás com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="4868b-284">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="4868b-285">*Contador.navalha:*</span><span class="sxs-lookup"><span data-stu-id="4868b-285">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="4868b-286">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="4868b-286">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="4868b-287">Adicione todos os namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="4868b-287">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="4868b-288">Os espaços de nome típicos usados pelos componentes da Navalha incluem:</span><span class="sxs-lookup"><span data-stu-id="4868b-288">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="4868b-289">Especifique uma classe base</span><span class="sxs-lookup"><span data-stu-id="4868b-289">Specify a base class</span></span>

<span data-ttu-id="4868b-290">A [`@inherits`](xref:mvc/views/razor#inherits) diretiva pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-290">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="4868b-291">O exemplo a seguir mostra como um `BlazorRocksBase`componente pode herdar uma classe base, para fornecer as propriedades e métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-291">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="4868b-292">A classe base `ComponentBase`deve derivar de .</span><span class="sxs-lookup"><span data-stu-id="4868b-292">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="4868b-293">*Páginas/BlazorRocks.razor:*</span><span class="sxs-lookup"><span data-stu-id="4868b-293">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="4868b-294">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="4868b-294">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="4868b-295">Especifique um atributo</span><span class="sxs-lookup"><span data-stu-id="4868b-295">Specify an attribute</span></span>

<span data-ttu-id="4868b-296">Os atributos podem ser especificados [`@attribute`](xref:mvc/views/razor#attribute) em componentes de Navalha com a diretiva.</span><span class="sxs-lookup"><span data-stu-id="4868b-296">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="4868b-297">O exemplo a `[Authorize]` seguir aplica o atributo à classe de componentes:</span><span class="sxs-lookup"><span data-stu-id="4868b-297">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="4868b-298">Componentes de importação</span><span class="sxs-lookup"><span data-stu-id="4868b-298">Import components</span></span>

<span data-ttu-id="4868b-299">O namespace de um componente de autoria com Razor é baseado em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="4868b-299">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="4868b-300">[`@namespace`](xref:mvc/views/razor#namespace)designação em Razor file *(.razor*) markup ().`@namespace BlazorSample.MyNamespace`</span><span class="sxs-lookup"><span data-stu-id="4868b-300">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="4868b-301">O projeto `RootNamespace` está no arquivo`<RootNamespace>BlazorSample</RootNamespace>`do projeto ( . .</span><span class="sxs-lookup"><span data-stu-id="4868b-301">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="4868b-302">O nome do projeto, retirado do nome do arquivo do arquivo do projeto (*.csproj),* e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-302">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="4868b-303">Por exemplo, o framework resolve *{PROJECT ROOT}/Pages/Index.razor* *(BlazorSample.csproj)* para o namespace `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="4868b-303">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="4868b-304">Os componentes seguem as regras de vinculação de nomes C#.</span><span class="sxs-lookup"><span data-stu-id="4868b-304">Components follow C# name binding rules.</span></span> <span data-ttu-id="4868b-305">Para `Index` o componente neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="4868b-305">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="4868b-306">Na mesma pasta, *Páginas*.</span><span class="sxs-lookup"><span data-stu-id="4868b-306">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="4868b-307">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="4868b-307">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="4868b-308">Os componentes definidos em um namespace diferente [`@using`](xref:mvc/views/razor#using) são colocados em escopo usando a diretiva de Razor.</span><span class="sxs-lookup"><span data-stu-id="4868b-308">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="4868b-309">Se houver `NavMenu.razor`outro componente na pasta *BlazorSample/Shared/,* o `Index.razor` componente poderá `@using` ser usado com a seguinte declaração:</span><span class="sxs-lookup"><span data-stu-id="4868b-309">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="4868b-310">Os componentes também podem ser referenciados usando seus nomes [`@using`](xref:mvc/views/razor#using) totalmente qualificados, o que não requer a diretiva:</span><span class="sxs-lookup"><span data-stu-id="4868b-310">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="4868b-311">A `global::` qualificação não é suportada.</span><span class="sxs-lookup"><span data-stu-id="4868b-311">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="4868b-312">A importação de componentes `using` com instruções `@using Foo = Bar`alias (por exemplo) não é suportada.</span><span class="sxs-lookup"><span data-stu-id="4868b-312">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="4868b-313">Nomes parcialmente qualificados não são suportados.</span><span class="sxs-lookup"><span data-stu-id="4868b-313">Partially qualified names aren't supported.</span></span> <span data-ttu-id="4868b-314">Por exemplo, `@using BlazorSample` adicionar `NavMenu.razor` e `<Shared.NavMenu></Shared.NavMenu>` referenciar não é suportado.</span><span class="sxs-lookup"><span data-stu-id="4868b-314">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="4868b-315">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="4868b-315">Conditional HTML element attributes</span></span>

<span data-ttu-id="4868b-316">Os atributos do elemento HTML são validamente renderizados com base no valor .NET.</span><span class="sxs-lookup"><span data-stu-id="4868b-316">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="4868b-317">Se o `false` valor `null`for ou , o atributo não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="4868b-317">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="4868b-318">Se o `true`valor for, o atributo será minimizado.</span><span class="sxs-lookup"><span data-stu-id="4868b-318">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="4868b-319">No exemplo a `IsCompleted` seguir, `checked` determina se é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="4868b-319">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="4868b-320">Se `IsCompleted` `true`for, a caixa de seleção é renderizada como:</span><span class="sxs-lookup"><span data-stu-id="4868b-320">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="4868b-321">Se `IsCompleted` `false`for, a caixa de seleção é renderizada como:</span><span class="sxs-lookup"><span data-stu-id="4868b-321">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="4868b-322">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="4868b-322">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="4868b-323">Alguns atributos HTML, como [pressionado por árias,](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)não funcionam `bool`corretamente quando o tipo .NET é um .</span><span class="sxs-lookup"><span data-stu-id="4868b-323">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="4868b-324">Nesses casos, use `string` um tipo `bool`em vez de um .</span><span class="sxs-lookup"><span data-stu-id="4868b-324">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="4868b-325">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="4868b-325">Raw HTML</span></span>

<span data-ttu-id="4868b-326">As strings são normalmente renderizadas usando nós de texto DOM, o que significa que qualquer marcação que possam conter é ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="4868b-326">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="4868b-327">Para renderizar HTML bruto, enrole o conteúdo HTML em um `MarkupString` valor.</span><span class="sxs-lookup"><span data-stu-id="4868b-327">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="4868b-328">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="4868b-328">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="4868b-329">Renderizar HTML bruto construído a partir de qualquer fonte não confiável é um risco de **segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="4868b-329">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="4868b-330">O exemplo a `MarkupString` seguir mostra usar o tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-330">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="4868b-331">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="4868b-331">Cascading values and parameters</span></span>

<span data-ttu-id="4868b-332">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componentes,](#component-parameters)especialmente quando há várias camadas de componentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-332">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="4868b-333">Os valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor a todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-333">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="4868b-334">Os valores e parâmetros em cascata também fornecem uma abordagem para os componentes coordenarem.</span><span class="sxs-lookup"><span data-stu-id="4868b-334">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="4868b-335">Exemplo temático</span><span class="sxs-lookup"><span data-stu-id="4868b-335">Theme example</span></span>

<span data-ttu-id="4868b-336">No exemplo a seguir do `ThemeInfo` aplicativo de exemplo, a classe especifica as informações do tema para fluir pela hierarquia do componente para que todos os botões dentro de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="4868b-336">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="4868b-337">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="4868b-337">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="4868b-338">Um componente ancestral pode fornecer um valor em cascata usando o componente Valor em Cascata.</span><span class="sxs-lookup"><span data-stu-id="4868b-338">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="4868b-339">O `CascadingValue` componente envolve uma subárvore da hierarquia de componentes e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="4868b-339">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="4868b-340">Por exemplo, o aplicativo de amostra`ThemeInfo`especifica as informações temáticas ( ) em um dos layouts do aplicativo como `@Body` um parâmetro em cascata para todos os componentes que compõem o corpo de layout da propriedade.</span><span class="sxs-lookup"><span data-stu-id="4868b-340">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="4868b-341">`ButtonClass`é atribuído um `btn-success` valor do componente de layout.</span><span class="sxs-lookup"><span data-stu-id="4868b-341">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="4868b-342">Qualquer componente descendente pode consumir `ThemeInfo` esta propriedade através do objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="4868b-342">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="4868b-343">`CascadingValuesParametersLayout`Componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-343">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="4868b-344">Para fazer uso de valores em cascata, os `[CascadingParameter]` componentes declaram parâmetros em cascata usando o atributo.</span><span class="sxs-lookup"><span data-stu-id="4868b-344">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="4868b-345">Os valores em cascata estão vinculados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="4868b-345">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="4868b-346">No aplicativo de `CascadingValuesParametersTheme` amostra, o `ThemeInfo` componente liga o valor em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="4868b-346">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="4868b-347">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-347">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="4868b-348">`CascadingValuesParametersTheme`Componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-348">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

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
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="4868b-349">Para cascata de múltiplos valores do mesmo tipo `Name` dentro da `CascadingValue` mesma subárvore, forneça uma seqüência única para cada componente e seu correspondente `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="4868b-349">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="4868b-350">No exemplo a `CascadingValue` seguir, dois componentes `MyCascadingType` cascatam diferentes instâncias de nome:</span><span class="sxs-lookup"><span data-stu-id="4868b-350">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="4868b-351">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="4868b-351">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="4868b-352">Exemplo de Set de guia</span><span class="sxs-lookup"><span data-stu-id="4868b-352">TabSet example</span></span>

<span data-ttu-id="4868b-353">Os parâmetros em cascata também permitem que os componentes colaborem em toda a hierarquia de componentes.</span><span class="sxs-lookup"><span data-stu-id="4868b-353">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="4868b-354">Por exemplo, considere o seguinte exemplo *de TabSet* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="4868b-354">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="4868b-355">O aplicativo de `ITab` exemplo tem uma interface que implementa as guias:</span><span class="sxs-lookup"><span data-stu-id="4868b-355">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="4868b-356">O `CascadingValuesParametersTabSet` componente `TabSet` usa o componente, que contém vários `Tab` componentes:</span><span class="sxs-lookup"><span data-stu-id="4868b-356">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="4868b-357">Os `Tab` componentes da criança não são explicitamente `TabSet`passados como parâmetros para o .</span><span class="sxs-lookup"><span data-stu-id="4868b-357">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="4868b-358">Em vez `Tab` disso, os componentes da `TabSet`criança fazem parte do conteúdo infantil do .</span><span class="sxs-lookup"><span data-stu-id="4868b-358">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="4868b-359">No entanto, o `TabSet` ainda `Tab` precisa saber sobre cada componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação `TabSet` sem exigir código adicional, o componente *pode fornecer-se como um valor em cascata* que é então captado pelos componentes descendentes. `Tab`</span><span class="sxs-lookup"><span data-stu-id="4868b-359">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="4868b-360">`TabSet`Componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-360">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="4868b-361">`Tab` Os componentes descendentes capturam o contendo `TabSet` como um parâmetro `Tab` em cascata, `TabSet` de modo que os componentes se adicionam à e coordenam em qual guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="4868b-361">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="4868b-362">`Tab`Componente:</span><span class="sxs-lookup"><span data-stu-id="4868b-362">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="4868b-363">Modelos de navalha</span><span class="sxs-lookup"><span data-stu-id="4868b-363">Razor templates</span></span>

<span data-ttu-id="4868b-364">Os fragmentos de renderização podem ser definidos usando a sintaxe do modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="4868b-364">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="4868b-365">Os modelos de navalha são uma maneira de definir um trecho de ui e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="4868b-365">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="4868b-366">O exemplo a seguir `RenderFragment` ilustra `RenderFragment<T>` como especificar e valores e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="4868b-366">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="4868b-367">Fragmentos de renderização também podem ser passados como argumentos para [componentes modelados](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="4868b-367">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="4868b-368">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="4868b-368">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="4868b-369">Imagens scalable vector graphics (SVG)</span><span class="sxs-lookup"><span data-stu-id="4868b-369">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="4868b-370">Uma Blazor vez que renderizações HTML, imagens suportadas pelo navegador, incluindo imagens SCalable Vector `<img>` Graphics (SVG),*.svg*são suportadas através da tag:</span><span class="sxs-lookup"><span data-stu-id="4868b-370">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="4868b-371">Da mesma forma, as imagens SVG são suportadas nas regras CSS de um arquivo de folha de estilos *(.css*):</span><span class="sxs-lookup"><span data-stu-id="4868b-371">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="4868b-372">No entanto, a marcação SVG inline não é suportada em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="4868b-372">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="4868b-373">Se você `<svg>` colocar uma tag diretamente em um arquivo de componente *(.razor),* a renderização básica de imagem será suportada, mas muitos cenários avançados ainda não são suportados.</span><span class="sxs-lookup"><span data-stu-id="4868b-373">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="4868b-374">Por exemplo, `<use>` as tags não são `@bind` respeitadas no momento e não podem ser usadas com algumas tags SVG.</span><span class="sxs-lookup"><span data-stu-id="4868b-374">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="4868b-375">Esperamos abordar essas limitações em um lançamento futuro.</span><span class="sxs-lookup"><span data-stu-id="4868b-375">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4868b-376">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4868b-376">Additional resources</span></span>

* <span data-ttu-id="4868b-377"><xref:security/blazor/server>&ndash; Inclui orientação Blazor sobre a construção de aplicativos do Servidor que devem enfrentar a exaustão de recursos.</span><span class="sxs-lookup"><span data-stu-id="4868b-377"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
