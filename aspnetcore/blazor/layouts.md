---
title: layouts Blazor do Núcleo ASP.NET
author: guardrex
description: Aprenda a criar componentes de Blazor layout reutilizáveis para aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660408"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="98472-103">layouts Blazor do Núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="98472-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="98472-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98472-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="98472-105">Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente fazem parte do layout geral do aplicativo e são usados por todos os componentes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="98472-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="98472-106">Copiar o código desses elementos em todos os componentes de&mdash;um aplicativo não é uma abordagem eficiente toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="98472-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="98472-107">Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="98472-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="98472-108">*Layouts* resolvem esse problema.</span><span class="sxs-lookup"><span data-stu-id="98472-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="98472-109">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="98472-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="98472-110">Um layout é definido em um modelo de Navalha ou em código C# e pode usar [vinculação de dados,](xref:blazor/data-binding) [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componentes.</span><span class="sxs-lookup"><span data-stu-id="98472-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="98472-111">Para transformar um *componente* em um *layout,* o componente:</span><span class="sxs-lookup"><span data-stu-id="98472-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="98472-112">Herda `LayoutComponentBase`, que define `Body` uma propriedade para o conteúdo renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="98472-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="98472-113">Usa a sintaxe `@Body` Razor para especificar o local na marcação do layout onde o conteúdo é renderizado.</span><span class="sxs-lookup"><span data-stu-id="98472-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="98472-114">A amostra de código a seguir mostra o modelo Razor de um componente de layout, *MainLayout.razor*.</span><span class="sxs-lookup"><span data-stu-id="98472-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="98472-115">O layout `LayoutComponentBase` herda `@Body` e define entre a barra de navegação e o rodapé:</span><span class="sxs-lookup"><span data-stu-id="98472-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="98472-116">Em um aplicativo baseado Blazor em um dos `MainLayout` modelos de aplicativo, o componente (*MainLayout.razor*) está na pasta *Compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="98472-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="98472-117">Layout padrão</span><span class="sxs-lookup"><span data-stu-id="98472-117">Default layout</span></span>

<span data-ttu-id="98472-118">Especifique o `Router` layout padrão do aplicativo no componente no arquivo *App.razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="98472-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="98472-119">O `Router` componente a seguir, fornecido Blazor pelos modelos padrão, define `MainLayout` o layout padrão para o componente:</span><span class="sxs-lookup"><span data-stu-id="98472-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="98472-120">Para fornecer um `NotFound` layout padrão `LayoutView` para `NotFound` conteúdo, especifique um para conteúdo:</span><span class="sxs-lookup"><span data-stu-id="98472-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="98472-121">Para obter mais `Router` informações <xref:blazor/routing>sobre o componente, consulte .</span><span class="sxs-lookup"><span data-stu-id="98472-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="98472-122">Especificar o layout como um layout padrão no roteador é uma prática útil porque ele pode ser substituído por componente ou por pasta.</span><span class="sxs-lookup"><span data-stu-id="98472-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="98472-123">Prefira usar o roteador para definir o layout padrão do aplicativo porque é a técnica mais geral.</span><span class="sxs-lookup"><span data-stu-id="98472-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="98472-124">Especifique um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="98472-124">Specify a layout in a component</span></span>

<span data-ttu-id="98472-125">Use a `@layout` diretiva Razor para aplicar um layout a um componente.</span><span class="sxs-lookup"><span data-stu-id="98472-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="98472-126">O compilador converte-se em um `@layout` `LayoutAttribute`, que é aplicado à classe de componentes.</span><span class="sxs-lookup"><span data-stu-id="98472-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="98472-127">O conteúdo do `MasterList` seguinte componente é `MasterLayout` inserido na `@Body`posição de :</span><span class="sxs-lookup"><span data-stu-id="98472-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="98472-128">Especificar o layout diretamente em um componente substitui um layout `@layout` *padrão* definido no roteador ou uma diretiva importada de *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="98472-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="98472-129">Seleção centralizada de layout</span><span class="sxs-lookup"><span data-stu-id="98472-129">Centralized layout selection</span></span>

<span data-ttu-id="98472-130">Cada pasta de um aplicativo pode conter opcionalmente um arquivo de modelo chamado *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="98472-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="98472-131">O compilador inclui as diretivas especificadas no arquivo de importações em todos os modelos de Barbear na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="98472-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="98472-132">Portanto, um *arquivo _Imports.razor* contendo `@layout MyCoolLayout` garante que todos os componentes em uma pasta utilizem `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="98472-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="98472-133">Não há necessidade de adicionar `@layout MyCoolLayout` repetidamente a todos os arquivos *.razor* dentro da pasta e subpastas.</span><span class="sxs-lookup"><span data-stu-id="98472-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="98472-134">`@using`diretivas também são aplicadas aos componentes da mesma forma.</span><span class="sxs-lookup"><span data-stu-id="98472-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="98472-135">As seguintes importações de arquivos *_Imports.razor:*</span><span class="sxs-lookup"><span data-stu-id="98472-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="98472-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="98472-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="98472-137">Todos os componentes razor na mesma pasta e quaisquer subpastas.</span><span class="sxs-lookup"><span data-stu-id="98472-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="98472-138">O namespace `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="98472-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="98472-139">O arquivo *_Imports.razor* é semelhante ao [arquivo _ViewImports.cshtml para visualizações e páginas de Razor,](xref:mvc/views/layout#importing-shared-directives) mas aplicado especificamente aos arquivos de componentes razor.</span><span class="sxs-lookup"><span data-stu-id="98472-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="98472-140">Especificar um layout em *_Imports.razor* substitui um layout especificado como layout padrão do *roteador*.</span><span class="sxs-lookup"><span data-stu-id="98472-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="98472-141">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="98472-141">Nested layouts</span></span>

<span data-ttu-id="98472-142">Os aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="98472-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="98472-143">Um componente pode referenciar um layout que, por sua vez, faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="98472-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="98472-144">Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.</span><span class="sxs-lookup"><span data-stu-id="98472-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="98472-145">O exemplo a seguir mostra como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="98472-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="98472-146">O arquivo *EpisodesComponent.razor* é o componente a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="98472-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="98472-147">O componente faz `MasterListLayout`referência ao :</span><span class="sxs-lookup"><span data-stu-id="98472-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="98472-148">O arquivo *MasterListLayout.razor* fornece o `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="98472-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="98472-149">O layout faz referência `MasterLayout`a outro layout, onde é renderizado.</span><span class="sxs-lookup"><span data-stu-id="98472-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="98472-150">`EpisodesComponent`é renderizado `@Body` onde aparece:</span><span class="sxs-lookup"><span data-stu-id="98472-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="98472-151">Finalmente, `MasterLayout` em *MasterLayout.razor* contém os elementos de layout de nível superior, como o cabeçalho, menu principal e rodapé.</span><span class="sxs-lookup"><span data-stu-id="98472-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="98472-152">`MasterListLayout`com `EpisodesComponent` o é `@Body` renderizado onde aparece:</span><span class="sxs-lookup"><span data-stu-id="98472-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="98472-153">Compartilhe um layout de Páginas de Navalha com componentes integrados</span><span class="sxs-lookup"><span data-stu-id="98472-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="98472-154">Quando os componentes rotáveis são integrados em um aplicativo Razor Pages, o layout compartilhado do aplicativo pode ser usado com os componentes.</span><span class="sxs-lookup"><span data-stu-id="98472-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="98472-155">Para obter mais informações, consulte <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="98472-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98472-156">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="98472-156">Additional resources</span></span>

* <xref:mvc/views/layout>
