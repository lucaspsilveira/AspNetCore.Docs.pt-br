---
title: BlazorLayouts de ASP.NET Core
author: guardrex
description: Saiba como criar componentes de layout reutilizáveis para Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: fe78a144b413bf97be83d20b11148e1856608f78
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944224"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="a7770-103">BlazorLayouts de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7770-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="a7770-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a7770-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a7770-105">Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7770-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="a7770-106">Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem eficiente.</span><span class="sxs-lookup"><span data-stu-id="a7770-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="a7770-107">Toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="a7770-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="a7770-108">Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="a7770-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="a7770-109">Os *layouts* resolvem esse problema.</span><span class="sxs-lookup"><span data-stu-id="a7770-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="a7770-110">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="a7770-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="a7770-111">Um layout é definido em um Razor modelo ou em código C# e pode usar [Associação de dados](xref:blazor/components/data-binding), [injeção de dependência](xref:blazor/fundamentals/dependency-injection)e outros cenários de componente.</span><span class="sxs-lookup"><span data-stu-id="a7770-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="a7770-112">Para transformar um *componente* em um *layout*, o componente:</span><span class="sxs-lookup"><span data-stu-id="a7770-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="a7770-113">Herda de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , que define uma <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriedade para o conteúdo renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="a7770-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="a7770-114">Usa a Razor sintaxe `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.</span><span class="sxs-lookup"><span data-stu-id="a7770-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="a7770-115">O exemplo de código a seguir mostra o Razor modelo de um componente de layout, `MainLayout.razor` .</span><span class="sxs-lookup"><span data-stu-id="a7770-115">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="a7770-116">O layout herda <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> e define o `@Body` entre a barra de navegação e o rodapé:</span><span class="sxs-lookup"><span data-stu-id="a7770-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="a7770-117">Em um aplicativo baseado em um dos Blazor modelos de aplicativo, o `MainLayout` componente ( `MainLayout.razor` ) está na pasta do aplicativo `Shared` .</span><span class="sxs-lookup"><span data-stu-id="a7770-117">In an app based on one of the Blazor app templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="a7770-118">Layout padrão</span><span class="sxs-lookup"><span data-stu-id="a7770-118">Default layout</span></span>

<span data-ttu-id="a7770-119">Especifique o layout do aplicativo padrão no <xref:Microsoft.AspNetCore.Components.Routing.Router> componente no arquivo do aplicativo `App.razor` .</span><span class="sxs-lookup"><span data-stu-id="a7770-119">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="a7770-120">O componente a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router> , que é fornecido pelos Blazor modelos padrão, define o layout padrão para o `MainLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="a7770-120">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="a7770-121">Para fornecer um layout padrão para <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, especifique um <xref:Microsoft.AspNetCore.Components.LayoutView> para o <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo:</span><span class="sxs-lookup"><span data-stu-id="a7770-121">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="a7770-122">Para obter mais informações sobre o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, consulte <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="a7770-122">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="a7770-123">A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta.</span><span class="sxs-lookup"><span data-stu-id="a7770-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="a7770-124">Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.</span><span class="sxs-lookup"><span data-stu-id="a7770-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="a7770-125">Especificar um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="a7770-125">Specify a layout in a component</span></span>

<span data-ttu-id="a7770-126">Use a Razor diretiva `@layout` para aplicar um layout a um componente.</span><span class="sxs-lookup"><span data-stu-id="a7770-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="a7770-127">O compilador converte `@layout` em um <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , que é aplicado à classe de componente.</span><span class="sxs-lookup"><span data-stu-id="a7770-127">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="a7770-128">O conteúdo do componente a seguir `MasterList` é inserido no `MasterLayout` na posição de `@Body` :</span><span class="sxs-lookup"><span data-stu-id="a7770-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="a7770-129">Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma `@layout` diretiva importada do `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="a7770-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="a7770-130">Seleção de layout centralizado</span><span class="sxs-lookup"><span data-stu-id="a7770-130">Centralized layout selection</span></span>

<span data-ttu-id="a7770-131">Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="a7770-131">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="a7770-132">O compilador inclui as diretivas especificadas no arquivo Imports em todos os Razor modelos na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="a7770-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="a7770-133">Portanto, um `_Imports.razor` arquivo que contém `@layout MyCoolLayout` garante que todos os componentes em uma pasta usem o `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="a7770-133">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="a7770-134">Não há necessidade de adicionar repetidamente `@layout MyCoolLayout` a todos os `.razor` arquivos dentro da pasta e subpastas.</span><span class="sxs-lookup"><span data-stu-id="a7770-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="a7770-135">`@using`as diretivas também são aplicadas aos componentes da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="a7770-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="a7770-136">As seguintes `_Imports.razor` importações de arquivo:</span><span class="sxs-lookup"><span data-stu-id="a7770-136">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="a7770-137">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="a7770-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="a7770-138">Todos os Razor componentes na mesma pasta e em todas as subpastas.</span><span class="sxs-lookup"><span data-stu-id="a7770-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="a7770-139">O namespace `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="a7770-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="a7770-140">O `_Imports.razor` arquivo é semelhante ao [arquivo _ViewImports. cshtml para Razor exibições e páginas](xref:mvc/views/layout#importing-shared-directives) , mas é aplicado especificamente a Razor arquivos de componente.</span><span class="sxs-lookup"><span data-stu-id="a7770-140">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="a7770-141">A especificação de um layout em `_Imports.razor` substitui um layout especificado como o *Layout padrão*do roteador.</span><span class="sxs-lookup"><span data-stu-id="a7770-141">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="a7770-142">**Não** adicione uma Razor `@layout` diretiva ao `_Imports.razor` arquivo raiz, o que resulta em um loop infinito de layouts no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7770-142">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="a7770-143">Para controlar o layout do aplicativo padrão, especifique o layout no `Router` componente.</span><span class="sxs-lookup"><span data-stu-id="a7770-143">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="a7770-144">Para obter mais informações, consulte a seção [Layout padrão](#default-layout) .</span><span class="sxs-lookup"><span data-stu-id="a7770-144">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="a7770-145">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="a7770-145">Nested layouts</span></span>

<span data-ttu-id="a7770-146">Os aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="a7770-146">Apps can consist of nested layouts.</span></span> <span data-ttu-id="a7770-147">Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="a7770-147">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="a7770-148">Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.</span><span class="sxs-lookup"><span data-stu-id="a7770-148">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="a7770-149">O exemplo a seguir mostra como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="a7770-149">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="a7770-150">O `EpisodesComponent.razor` arquivo é o componente a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="a7770-150">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="a7770-151">O componente faz referência a `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="a7770-151">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="a7770-152">O `MasterListLayout.razor` arquivo fornece o `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="a7770-152">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="a7770-153">O layout faz referência a outro layout, `MasterLayout` , onde é renderizado.</span><span class="sxs-lookup"><span data-stu-id="a7770-153">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="a7770-154">`EpisodesComponent`é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="a7770-154">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="a7770-155">Por fim, `MasterLayout` no `MasterLayout.razor` contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé.</span><span class="sxs-lookup"><span data-stu-id="a7770-155">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="a7770-156">`MasterListLayout`com o `EpisodesComponent` é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="a7770-156">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="a7770-157">Compartilhar um Razor layout de páginas com componentes integrados</span><span class="sxs-lookup"><span data-stu-id="a7770-157">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="a7770-158">Quando componentes roteáveis são integrados a um Razor aplicativo de páginas, o layout compartilhado do aplicativo pode ser usado com os componentes.</span><span class="sxs-lookup"><span data-stu-id="a7770-158">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="a7770-159">Para obter mais informações, consulte <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="a7770-159">For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7770-160">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a7770-160">Additional resources</span></span>

* <xref:mvc/views/layout>
