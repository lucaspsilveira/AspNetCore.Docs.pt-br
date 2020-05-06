---
title: Auxiliar de marca parcial no ASP.NET Core
author: scottaddie
description: Descubra o auxiliar de marca parcial do ASP.NET Core e a função de cada um de seus atributos na renderização de uma exibição parcial.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: d3207969dfbeb9a81e0da88f38a38c6889bbfba9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775863"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="92187-103">Auxiliar de marca parcial no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92187-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="92187-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="92187-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="92187-105">Para obter uma visão geral de Auxiliares de marcação, consulte <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="92187-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="92187-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="92187-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="92187-107">Visão geral</span><span class="sxs-lookup"><span data-stu-id="92187-107">Overview</span></span>

<span data-ttu-id="92187-108">O auxiliar de marca parcial é usado para renderizar uma [exibição parcial](xref:mvc/views/partial) em Razor páginas e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="92187-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="92187-109">Considere que ele:</span><span class="sxs-lookup"><span data-stu-id="92187-109">Consider that it:</span></span>

* <span data-ttu-id="92187-110">Exige o ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="92187-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="92187-111">É uma alternativa à [sintaxe do HTML Helper](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="92187-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="92187-112">Renderiza a exibição parcial de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="92187-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="92187-113">As opções do HTML Helper para renderizar uma exibição parcial incluem:</span><span class="sxs-lookup"><span data-stu-id="92187-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="92187-114">O modelo *Product* é usado nos exemplos ao longo deste documento:</span><span class="sxs-lookup"><span data-stu-id="92187-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="92187-115">Segue um inventário dos atributos do auxiliar de marca parcial.</span><span class="sxs-lookup"><span data-stu-id="92187-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="92187-116">name</span><span class="sxs-lookup"><span data-stu-id="92187-116">name</span></span>

<span data-ttu-id="92187-117">O atributo `name` é necessário.</span><span class="sxs-lookup"><span data-stu-id="92187-117">The `name` attribute is required.</span></span> <span data-ttu-id="92187-118">Indica o nome ou o caminho da exibição parcial a ser renderizada.</span><span class="sxs-lookup"><span data-stu-id="92187-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="92187-119">Quando é fornecido um nome de exibição parcial, o processo [descoberta de exibição](xref:mvc/views/overview#view-discovery) é iniciado.</span><span class="sxs-lookup"><span data-stu-id="92187-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="92187-120">Esse processo é ignorado quando um caminho explícito é fornecido.</span><span class="sxs-lookup"><span data-stu-id="92187-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="92187-121">Para todos os valores de `name` aceitáveis, consulte [Descoberta de exibição parcial](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="92187-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="92187-122">A marcação a seguir usa um caminho explícito, indicando que *_ProductPartial.cshtml* deve ser carregado da pasta *Compartilhado*.</span><span class="sxs-lookup"><span data-stu-id="92187-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="92187-123">Usando o atributo [for](#for), um modelo é passado para a exibição parcial para associação.</span><span class="sxs-lookup"><span data-stu-id="92187-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="92187-124">for</span><span class="sxs-lookup"><span data-stu-id="92187-124">for</span></span>

<span data-ttu-id="92187-125">O atributo `for` atribui uma [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) a ser avaliada em relação ao modelo atual.</span><span class="sxs-lookup"><span data-stu-id="92187-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="92187-126">Uma `ModelExpression` infere a sintaxe `@Model.`.</span><span class="sxs-lookup"><span data-stu-id="92187-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="92187-127">Por exemplo, `for="Product"` pode ser usado em vez de `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="92187-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="92187-128">Esse comportamento de inferência padrão é substituído usando o símbolo `@` para definir uma expressão embutida.</span><span class="sxs-lookup"><span data-stu-id="92187-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="92187-129">A seguinte marcação carrega *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="92187-129">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="92187-130">A exibição parcial é associada à propriedade `Product` do modelo de página associado:</span><span class="sxs-lookup"><span data-stu-id="92187-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="92187-131">modelo</span><span class="sxs-lookup"><span data-stu-id="92187-131">model</span></span>

<span data-ttu-id="92187-132">O atributo `model` atribui uma instância de modelo a ser passada para a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="92187-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="92187-133">O atributo `model` não pode ser usado com o atributo [for](#for).</span><span class="sxs-lookup"><span data-stu-id="92187-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="92187-134">Na marcação a seguir, é criada uma nova instância do objeto `Product` que é passada ao atributo `model` para associação:</span><span class="sxs-lookup"><span data-stu-id="92187-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="92187-135">view-data</span><span class="sxs-lookup"><span data-stu-id="92187-135">view-data</span></span>

<span data-ttu-id="92187-136">O atributo `view-data` atribui um [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) a ser passado para a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="92187-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="92187-137">A seguinte marcação faz com que toda a coleção ViewData fique acessível para a exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="92187-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="92187-138">No código anterior, o valor da chave `IsNumberReadOnly` é definido como `true` e adicionado à coleção ViewData.</span><span class="sxs-lookup"><span data-stu-id="92187-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="92187-139">Consequentemente, `ViewData["IsNumberReadOnly"]` se torna acessível dentro da exibição parcial a seguir:</span><span class="sxs-lookup"><span data-stu-id="92187-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="92187-140">Neste exemplo, o valor de `ViewData["IsNumberReadOnly"]` determina se o campo *Número* é exibido como somente leitura.</span><span class="sxs-lookup"><span data-stu-id="92187-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="92187-141">Migrar de um auxiliar HTML</span><span class="sxs-lookup"><span data-stu-id="92187-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="92187-142">Considere o seguinte exemplo de auxiliar HTML assíncrono.</span><span class="sxs-lookup"><span data-stu-id="92187-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="92187-143">Uma coleção de produtos é iterada e exibida.</span><span class="sxs-lookup"><span data-stu-id="92187-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="92187-144">Segundo o primeiro parâmetro do método `PartialAsync`, a exibição parcial *_ProductPartial.cshtml* é carregada.</span><span class="sxs-lookup"><span data-stu-id="92187-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="92187-145">Uma instância do modelo `Product` é passada para a exibição parcial para associação.</span><span class="sxs-lookup"><span data-stu-id="92187-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="92187-146">O auxiliar de marca parcial seguir alcança o mesmo comportamento de renderização assíncrona que o auxiliar HTML `PartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="92187-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="92187-147">Uma instância de modelo `Product` é atribuída ao atributo `model` para associação à exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="92187-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="92187-148">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="92187-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
