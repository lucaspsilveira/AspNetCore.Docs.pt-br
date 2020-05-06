---
title: Exibições parciais no ASP.NET Core
author: ardalis
description: Descubra como usar exibições parciais para dividir os arquivos de marcação grandes e reduzir a duplicação de marcações comuns em páginas da Web em aplicativos ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/partial
ms.openlocfilehash: 1bce6b9cdc876062b050eae6eb3c4acf0127ce92
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777118"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="4e2b4-103">Exibições parciais no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e2b4-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="4e2b4-104">Por [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="4e2b4-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="4e2b4-105">Uma exibição parcial é um [Razor](xref:mvc/views/razor) arquivo de marcação (*. cshtml*) que processa a saída HTML *dentro* de outra saída renderizada do arquivo de marcação.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4e2b4-106">O termo *exibição parcial* é usado ao desenvolver um aplicativo MVC, em que os arquivos de marcação são chamados de modos Razor de *exibição*ou um aplicativo de páginas, onde os arquivos de marcação são chamados *páginas*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="4e2b4-107">Este tópico se refere genericmente a exibições Razor do MVC e páginas de páginas como *arquivos de marcação*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="4e2b4-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4e2b4-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="4e2b4-109">Quando usar exibições parciais</span><span class="sxs-lookup"><span data-stu-id="4e2b4-109">When to use partial views</span></span>

<span data-ttu-id="4e2b4-110">Exibições parciais são uma maneira eficaz de:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="4e2b4-111">Dividir arquivos de marcação grandes em componentes menores.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="4e2b4-112">Aproveitar a vantagem de trabalhar com cada parte isolada em uma exibição parcial em um arquivo de marcação grande e complexo, composto por diversas partes lógicas.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="4e2b4-113">O código no arquivo de marcação é gerenciável porque a marcação contém somente a estrutura de página geral e as referências a exibições parciais.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="4e2b4-114">Reduzir a duplicação de conteúdo de marcação comum em arquivos de marcação.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="4e2b4-115">Quando os mesmos elementos de marcação são usados nos arquivos de marcação, uma exibição parcial remove a duplicação de conteúdo de marcação em um arquivo de exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="4e2b4-116">Quando a marcação é alterada na exibição parcial, ela atualiza a saída renderizada dos arquivos de marcação que usam a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="4e2b4-117">As exibições parciais não podem ser usadas para manter os elementos de layout comuns.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="4e2b4-118">Os elementos de layout comuns precisam ser especificados nos arquivos [_Layout.cshtml](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="4e2b4-119">Não use uma exibição parcial em que a lógica de renderização complexa ou a execução de código são necessárias para renderizar a marcação.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="4e2b4-120">Em vez de uma exibição parcial, use um [componente de exibição](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="4e2b4-121">Declarar exibições parciais</span><span class="sxs-lookup"><span data-stu-id="4e2b4-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="4e2b4-122">Uma exibição parcial é um arquivo de marcação *. cshtml* mantido dentro da pasta de *exibições* (MVC) ouRazor da pasta de *páginas* (páginas).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-122">A partial view is a *.cshtml* markup file maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="4e2b4-123">No ASP.NET Core MVC, um <xref:Microsoft.AspNetCore.Mvc.ViewResult> do controlador é capaz de retornar uma exibição ou uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="4e2b4-124">Em Razor páginas, um <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> pode retornar uma exibição parcial representada como um <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> objeto.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="4e2b4-125">A referência e a renderização de exibições parciais são descritas na seção [Referenciar uma exibição parcial](#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="4e2b4-126">Ao contrário da exibição do MVC ou renderização de página, uma exibição parcial não executa *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="4e2b4-127">Para obter mais informações sobre *_ViewStart.cshtml*, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="4e2b4-128">Nomes de arquivos de exibição parcial geralmente começam com um sublinhado (`_`).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="4e2b4-129">Essa convenção de nomenclatura não é obrigatória, mas ajuda a diferenciar visualmente as exibições parciais das exibições e das páginas.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4e2b4-130">Uma exibição parcial é um arquivo de marcação *.cshtml* mantido dentro da pasta *Exibições*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="4e2b4-131">Um <xref:Microsoft.AspNetCore.Mvc.ViewResult> do controlador é capaz de retornar uma exibição ou uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="4e2b4-132">A referência e a renderização de exibições parciais são descritas na seção [Referenciar uma exibição parcial](#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="4e2b4-133">Ao contrário da renderização de exibição do MVC, uma exibição parcial não executa *_ViewStart.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="4e2b4-134">Para obter mais informações sobre *_ViewStart.cshtml*, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="4e2b4-135">Nomes de arquivos de exibição parcial geralmente começam com um sublinhado (`_`).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="4e2b4-136">Essa convenção de nomenclatura não é obrigatória, mas ajuda a diferenciar visualmente as exibições parciais das exibições.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="4e2b4-137">Referenciar uma exibição parcial</span><span class="sxs-lookup"><span data-stu-id="4e2b4-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a><span data-ttu-id="4e2b4-138">Usar uma exibição parcial em Razor páginas PageModel</span><span class="sxs-lookup"><span data-stu-id="4e2b4-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="4e2b4-139">No ASP.NET Core 2,0 ou 2,1, o seguinte método de manipulador renderiza a \* \_exibição parcial AuthorPartialRP. cshtml\* para a resposta:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4e2b4-140">No ASP.NET Core 2.2 ou posterior, um método de manipulador pode, como alternativa, chamar o método <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> para produzir um objeto `PartialViewResult`:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="4e2b4-141">Usar uma exibição parcial em um arquivo de marcação</span><span class="sxs-lookup"><span data-stu-id="4e2b4-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4e2b4-142">Há várias maneiras de referenciar uma exibição parcial em um arquivo de marcação.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="4e2b4-143">É recomendável que os aplicativos usem uma das seguintes abordagens de renderização assíncrona:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="4e2b4-144">Auxiliar de marcação parcial</span><span class="sxs-lookup"><span data-stu-id="4e2b4-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="4e2b4-145">Auxiliar de HTML assíncrono</span><span class="sxs-lookup"><span data-stu-id="4e2b4-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="4e2b4-146">Há duas maneiras de referenciar uma exibição parcial em um arquivo de marcação:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="4e2b4-147">Auxiliar de HTML assíncrono</span><span class="sxs-lookup"><span data-stu-id="4e2b4-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="4e2b4-148">Auxiliar de HTML síncrono</span><span class="sxs-lookup"><span data-stu-id="4e2b4-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="4e2b4-149">É recomendável que os aplicativos usem o [Auxiliar de HTML assíncrono](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="4e2b4-150">Auxiliar de marca parcial</span><span class="sxs-lookup"><span data-stu-id="4e2b4-150">Partial Tag Helper</span></span>

<span data-ttu-id="4e2b4-151">O [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) exige o ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="4e2b4-152">O Auxiliar de Marca Parcial renderiza o conteúdo de forma assíncrona e usa uma sintaxe semelhante a HTML:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="4e2b4-153">Quando houver uma extensão de arquivo, o Auxiliar de Marca fará referência a uma exibição parcial que precisa estar na mesma pasta que o arquivo de marcação que chama a exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="4e2b4-154">O exemplo a seguir faz referência a uma exibição parcial da raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="4e2b4-155">Caminhos que começam com um til-barra (`~/`) ou uma barra (`/`) referem-se à raiz do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="4e2b4-156">**RazorPages**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="4e2b4-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="4e2b4-158">O exemplo a seguir faz referência a uma exibição parcial com um caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="4e2b4-159">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="4e2b4-160">Auxiliar HTML assíncrono</span><span class="sxs-lookup"><span data-stu-id="4e2b4-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="4e2b4-161">Ao usar um Auxiliar HTML, a melhor prática é usar <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="4e2b4-162">`PartialAsync` retorna um tipo <xref:Microsoft.AspNetCore.Html.IHtmlContent> encapsulado em um <xref:System.Threading.Tasks.Task%601>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="4e2b4-163">O método é referenciado prefixando a chamada em espera com um caractere `@`:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="4e2b4-164">Quando houver a extensão de arquivo, o Auxiliar de HTML fará referência a uma exibição parcial que precisa estar na mesma pasta que o arquivo de marcação que chama a exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="4e2b4-165">O exemplo a seguir faz referência a uma exibição parcial da raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="4e2b4-166">Caminhos que começam com um til-barra (`~/`) ou uma barra (`/`) referem-se à raiz do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4e2b4-167">**RazorPages**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="4e2b4-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="4e2b4-169">O exemplo a seguir faz referência a uma exibição parcial com um caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="4e2b4-170">Como alternativa, é possível renderizar uma exibição parcial com <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="4e2b4-171">Esse método não retorna um <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="4e2b4-172">Ele transmite a saída renderizada diretamente para a resposta.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="4e2b4-173">Como o método não retorna um resultado, ele deve ser chamado dentro de Razor um bloco de código:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="4e2b4-174">Como `RenderPartialAsync` transmite conteúdo renderizado, ele apresenta melhor desempenho em alguns cenários.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="4e2b4-175">Em situações cruciais para o desempenho, submeta a página a benchmark usando ambas as abordagens e use aquela que gera uma resposta mais rápida.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="4e2b4-176">Auxiliar de HTML assíncrono</span><span class="sxs-lookup"><span data-stu-id="4e2b4-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="4e2b4-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> e <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> são os equivalentes síncronos de `PartialAsync` e `RenderPartialAsync`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="4e2b4-178">Os equivalentes síncronos não são recomendados porque há cenários em que eles realizam deadlock.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="4e2b4-179">Os métodos síncronos estão programados para serem removidos em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e2b4-180">Se for necessário executar código, use um [componente de exibição](xref:mvc/views/view-components) em vez de uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4e2b4-181">Chamar `Partial` ou `RenderPartial` resulta em um aviso do analisador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="4e2b4-182">Por exemplo, a presença de `Partial` produz a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="4e2b4-183">Uso de IHtmlHelper.Partial pode resultar em deadlocks de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="4e2b4-184">Considere usar o Auxiliar de Marca &lt;parcial&gt; ou IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="4e2b4-185">Substitua as chamadas para `@Html.Partial` por `@await Html.PartialAsync` ou o [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="4e2b4-186">Para obter mais informações sobre a migração do auxiliar de marca parcial, consulte [Migrar de um auxiliar HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="4e2b4-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="4e2b4-187">Descoberta de exibição parcial</span><span class="sxs-lookup"><span data-stu-id="4e2b4-187">Partial view discovery</span></span>

<span data-ttu-id="4e2b4-188">Quando uma exibição parcial é referenciada pelo nome sem uma extensão de arquivo, os seguintes locais são pesquisados na ordem indicada:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4e2b4-189">**RazorPages**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-189">**Razor Pages**</span></span>

1. <span data-ttu-id="4e2b4-190">Pasta da página em execução no momento</span><span class="sxs-lookup"><span data-stu-id="4e2b4-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="4e2b4-191">Grafo do diretório acima da pasta da página</span><span class="sxs-lookup"><span data-stu-id="4e2b4-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="4e2b4-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="4e2b4-193">As convenções a seguir se aplicam à descoberta de exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="4e2b4-194">Diferentes exibições parciais com o mesmo nome de arquivo são permitidos quando as exibições parciais estão em pastas diferentes.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="4e2b4-195">Ao referenciar uma exibição parcial pelo nome sem uma extensão de arquivo e a exibição parcial está presente na pasta do chamador e na pasta *Compartilhada*, a exibição parcial na pasta do chamador fornece a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="4e2b4-196">Se a exibição parcial não existir na pasta do chamador, ela será fornecida pela pasta *Compartilhada*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="4e2b4-197">Exibições parciais na pasta *Compartilhada* são chamadas de *exibições parciais compartilhadas* ou *exibições parciais padrão*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="4e2b4-198">Exibições parciais podem ser *encadeadas*&mdash;uma exibição parcial pode chamar outra exibição parcial se uma referência circular não é formada pelas chamadas.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="4e2b4-199">Caminhos relativos sempre são relativos ao arquivo atual, não à raiz ou ao pai do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="4e2b4-200">Um [Razor](xref:mvc/views/razor) `section` definido em uma exibição parcial é invisível para arquivos de marcação pai.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="4e2b4-201">A `section` só é visível para a exibição parcial na qual ela está definida.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="4e2b4-202">Acessar dados de exibições parciais</span><span class="sxs-lookup"><span data-stu-id="4e2b4-202">Access data from partial views</span></span>

<span data-ttu-id="4e2b4-203">Quando uma exibição parcial é instanciada, ela recebe uma *cópia* do dicionário `ViewData` do pai.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="4e2b4-204">As atualizações feitas nos dados dentro da exibição parcial não são persistidas na exibição pai.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="4e2b4-205">Alterações a `ViewData` em uma exibição parcial são perdidas quando a exibição parcial retorna.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="4e2b4-206">O exemplo a seguir demonstra como passar uma instância de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) para uma exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="4e2b4-207">Você pode passar um modelo para uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="4e2b4-208">O modelo pode ser um objeto personalizado.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-208">The model can be a custom object.</span></span> <span data-ttu-id="4e2b4-209">Você pode passar um modelo com `PartialAsync` (renderiza um bloco de conteúdo para o chamador) ou `RenderPartialAsync` (transmite o conteúdo para a saída):</span><span class="sxs-lookup"><span data-stu-id="4e2b4-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4e2b4-210">**RazorPages**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-210">**Razor Pages**</span></span>

<span data-ttu-id="4e2b4-211">A marcação a seguir no aplicativo de exemplo é da página *Pages/ArticlesRP/ReadRP.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="4e2b4-212">A página contém duas exibições parciais.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-212">The page contains two partial views.</span></span> <span data-ttu-id="4e2b4-213">A segunda exibição parcial passa um modelo e `ViewData` para a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="4e2b4-214">A sobrecarga do construtor `ViewDataDictionary` é usada para passar um novo dicionário `ViewData`, retendo ainda o dicionário `ViewData` existente.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="4e2b4-215">*Pages/Shared/_AuthorPartialRP.cshtml* é a primeira exibição parcial referenciada pelo arquivo de marcação *ReadRP.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="4e2b4-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* é a segunda exibição parcial referenciada pelo arquivo de marcação *ReadRP.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="4e2b4-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="4e2b4-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="4e2b4-218">A marcação a seguir no aplicativo de exemplo mostra a exibição *Views/Articles/Read.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="4e2b4-219">A exibição contém duas exibições parciais.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-219">The view contains two partial views.</span></span> <span data-ttu-id="4e2b4-220">A segunda exibição parcial passa um modelo e `ViewData` para a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="4e2b4-221">A sobrecarga do construtor `ViewDataDictionary` é usada para passar um novo dicionário `ViewData`, retendo ainda o dicionário `ViewData` existente.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="4e2b4-222">*Views/Shared/_AuthorPartial. cshtml* é a primeira exibição parcial referenciada pelo arquivo de marcação *Read. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4e2b4-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="4e2b4-223">*Views/Articles/_ArticleSection.cshtml* é a segunda exibição parcial referenciada pelo arquivo de marcação *Read.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="4e2b4-224">No tempo de execução, as parciais são renderizadas para a saída renderizada do arquivo de marcação pai que, por sua vez, é renderizada dentro do *_Layout.cshtml* compartilhado.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="4e2b4-225">A primeira exibição parcial renderiza a data de publicação e o nome do autor do artigo:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="4e2b4-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="4e2b4-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="4e2b4-227">Esta exibição parcial do &lt;caminho do arquivo de exibição parcial compartilhada&gt;.</span><span class="sxs-lookup"><span data-stu-id="4e2b4-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="4e2b4-228">19/11/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="4e2b4-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="4e2b4-229">A segunda exibição parcial renderiza as seções do artigo:</span><span class="sxs-lookup"><span data-stu-id="4e2b4-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="4e2b4-230">Índice da seção um: 0</span><span class="sxs-lookup"><span data-stu-id="4e2b4-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="4e2b4-231">Pontuação de quatro e há sete anos...</span><span class="sxs-lookup"><span data-stu-id="4e2b4-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="4e2b4-232">Índice da seção dois: 1</span><span class="sxs-lookup"><span data-stu-id="4e2b4-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="4e2b4-233">Agora estamos envolvidos em uma grande guerra civil, testando...</span><span class="sxs-lookup"><span data-stu-id="4e2b4-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="4e2b4-234">Índice da seção três: 2</span><span class="sxs-lookup"><span data-stu-id="4e2b4-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="4e2b4-235">Mas, em um sentido mais amplo, não podemos dedicar...</span><span class="sxs-lookup"><span data-stu-id="4e2b4-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e2b4-236">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4e2b4-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
