---
title: BlazorLayouts de ASP.NET Core
author: guardrex
description: Saiba como criar componentes de layout reutilizáveis para Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 09cca9c4af23c35fdbc2ee92169913c960b0a68d
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424330"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="179de-103">BlazorLayouts de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="179de-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="179de-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="179de-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="179de-105">Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="179de-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="179de-106">Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem eficiente.</span><span class="sxs-lookup"><span data-stu-id="179de-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="179de-107">Toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="179de-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="179de-108">Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="179de-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="179de-109">Os *layouts* resolvem esse problema.</span><span class="sxs-lookup"><span data-stu-id="179de-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="179de-110">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="179de-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="179de-111">Um layout é definido em um Razor modelo ou em código C# e pode usar [Associação de dados](xref:blazor/data-binding), [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componente.</span><span class="sxs-lookup"><span data-stu-id="179de-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="179de-112">Para transformar um *componente* em um *layout*, o componente:</span><span class="sxs-lookup"><span data-stu-id="179de-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="179de-113">Herda de `LayoutComponentBase` , que define uma `Body` propriedade para o conteúdo renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="179de-113">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="179de-114">Usa a Razor sintaxe `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.</span><span class="sxs-lookup"><span data-stu-id="179de-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="179de-115">O exemplo de código a seguir mostra o Razor modelo de um componente de layout, *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="179de-115">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="179de-116">O layout herda `LayoutComponentBase` e define o `@Body` entre a barra de navegação e o rodapé:</span><span class="sxs-lookup"><span data-stu-id="179de-116">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="179de-117">Em um aplicativo baseado em um dos Blazor modelos de aplicativo, o `MainLayout` componente (*MainLayout. Razor*) está na pasta *compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="179de-117">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="179de-118">Layout padrão</span><span class="sxs-lookup"><span data-stu-id="179de-118">Default layout</span></span>

<span data-ttu-id="179de-119">Especifique o layout do aplicativo padrão no `Router` componente no arquivo *app. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="179de-119">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="179de-120">O componente a seguir `Router` , que é fornecido pelos Blazor modelos padrão, define o layout padrão para o `MainLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="179de-120">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="179de-121">Para fornecer um layout padrão para `NotFound` conteúdo, especifique um `LayoutView` para o `NotFound` conteúdo:</span><span class="sxs-lookup"><span data-stu-id="179de-121">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="179de-122">Para obter mais informações sobre o `Router` componente, consulte <xref:blazor/routing> .</span><span class="sxs-lookup"><span data-stu-id="179de-122">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="179de-123">A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta.</span><span class="sxs-lookup"><span data-stu-id="179de-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="179de-124">Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.</span><span class="sxs-lookup"><span data-stu-id="179de-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="179de-125">Especificar um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="179de-125">Specify a layout in a component</span></span>

<span data-ttu-id="179de-126">Use a Razor diretiva `@layout` para aplicar um layout a um componente.</span><span class="sxs-lookup"><span data-stu-id="179de-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="179de-127">O compilador converte `@layout` em um `LayoutAttribute` , que é aplicado à classe de componente.</span><span class="sxs-lookup"><span data-stu-id="179de-127">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="179de-128">O conteúdo do componente a seguir `MasterList` é inserido no `MasterLayout` na posição de `@Body` :</span><span class="sxs-lookup"><span data-stu-id="179de-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="179de-129">Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma `@layout` diretiva importada de *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="179de-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="179de-130">Seleção de layout centralizado</span><span class="sxs-lookup"><span data-stu-id="179de-130">Centralized layout selection</span></span>

<span data-ttu-id="179de-131">Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="179de-131">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="179de-132">O compilador inclui as diretivas especificadas no arquivo Imports em todos os Razor modelos na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="179de-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="179de-133">Portanto, um arquivo *_Imports. Razor* que contém `@layout MyCoolLayout` garante que todos os componentes em uma pasta usem o `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="179de-133">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="179de-134">Não há necessidade de adicionar repetidamente `@layout MyCoolLayout` a todos os arquivos *. Razor* dentro da pasta e subpastas.</span><span class="sxs-lookup"><span data-stu-id="179de-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="179de-135">`@using`as diretivas também são aplicadas aos componentes da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="179de-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="179de-136">As seguintes importações de arquivo *_Imports. Razor* :</span><span class="sxs-lookup"><span data-stu-id="179de-136">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="179de-137">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="179de-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="179de-138">Todos os Razor componentes na mesma pasta e em todas as subpastas.</span><span class="sxs-lookup"><span data-stu-id="179de-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="179de-139">O namespace `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="179de-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="179de-140">O arquivo *_Imports. Razor* é semelhante ao [arquivo _ViewImports. cshtml para Razor exibições e páginas](xref:mvc/views/layout#importing-shared-directives) , mas é aplicado especificamente a Razor arquivos de componente.</span><span class="sxs-lookup"><span data-stu-id="179de-140">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="179de-141">A especificação de um layout em *_Imports. Razor* substitui um layout especificado como o *Layout padrão*do roteador.</span><span class="sxs-lookup"><span data-stu-id="179de-141">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="179de-142">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="179de-142">Nested layouts</span></span>

<span data-ttu-id="179de-143">Os aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="179de-143">Apps can consist of nested layouts.</span></span> <span data-ttu-id="179de-144">Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="179de-144">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="179de-145">Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.</span><span class="sxs-lookup"><span data-stu-id="179de-145">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="179de-146">O exemplo a seguir mostra como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="179de-146">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="179de-147">O arquivo *EpisodesComponent. Razor* é o componente a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="179de-147">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="179de-148">O componente faz referência a `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="179de-148">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="179de-149">O arquivo *MasterListLayout. Razor* fornece o `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="179de-149">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="179de-150">O layout faz referência a outro layout, `MasterLayout` , onde é renderizado.</span><span class="sxs-lookup"><span data-stu-id="179de-150">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="179de-151">`EpisodesComponent`é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="179de-151">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="179de-152">Finalmente, `MasterLayout` no *MasterLayout. Razor* contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé.</span><span class="sxs-lookup"><span data-stu-id="179de-152">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="179de-153">`MasterListLayout`com o `EpisodesComponent` é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="179de-153">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="179de-154">Compartilhar um Razor layout de páginas com componentes integrados</span><span class="sxs-lookup"><span data-stu-id="179de-154">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="179de-155">Quando componentes roteáveis são integrados a um Razor aplicativo de páginas, o layout compartilhado do aplicativo pode ser usado com os componentes.</span><span class="sxs-lookup"><span data-stu-id="179de-155">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="179de-156">Para obter mais informações, consulte <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="179de-156">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="179de-157">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="179de-157">Additional resources</span></span>

* <xref:mvc/views/layout>
