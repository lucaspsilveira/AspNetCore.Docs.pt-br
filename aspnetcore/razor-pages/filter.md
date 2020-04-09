---
title: Métodos de filtro para Páginas Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como criar métodos de filtro para as Páginas Razor no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
uid: razor-pages/filter
ms.openlocfilehash: cd772da8ed565bc779d8c6bcc7c9949a0c1c7c60
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660751"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="8a402-103">Métodos de filtro para Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a402-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a402-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a402-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8a402-105">Os filtros de página Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Páginas Razor executem código antes e após a execução de um manipulador de Página Razor.</span><span class="sxs-lookup"><span data-stu-id="8a402-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="8a402-106">Os filtros de página Razor são semelhantes aos [filtros de ação de MVC do ASP.NET Core](xref:mvc/controllers/filters#action-filters), exceto que eles não podem ser aplicados aos métodos do manipulador de cada página individual.</span><span class="sxs-lookup"><span data-stu-id="8a402-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="8a402-107">Filtros de página Razor:</span><span class="sxs-lookup"><span data-stu-id="8a402-107">Razor Page filters:</span></span>

* <span data-ttu-id="8a402-108">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="8a402-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="8a402-109">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="8a402-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="8a402-110">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="8a402-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="8a402-111">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="8a402-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="8a402-112">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="8a402-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="8a402-113">Pode ter dependências de construtores povoadas por [Injeção de Dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="8a402-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8a402-114">Para obter mais informações, consulte [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) e [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="8a402-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="8a402-115">Enquanto os construtores de páginas e middleware permitem executar código personalizado antes da <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> execução de um método de manipulador, apenas os filtros de Página de Navalha permitem o acesso e a página.</span><span class="sxs-lookup"><span data-stu-id="8a402-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="8a402-116">Middleware tem acesso `HttpContext`ao , mas não ao "contexto da página".</span><span class="sxs-lookup"><span data-stu-id="8a402-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="8a402-117">Os filtros <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> possuem um parâmetro derivado, `HttpContext`que fornece acesso a .</span><span class="sxs-lookup"><span data-stu-id="8a402-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="8a402-118">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="8a402-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="8a402-119">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a402-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a402-120">Os filtros de página Razor fornecem os métodos a seguir, que podem ser aplicados globalmente ou no nível da página:</span><span class="sxs-lookup"><span data-stu-id="8a402-120">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="8a402-121">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="8a402-121">Synchronous methods:</span></span>

  * <span data-ttu-id="8a402-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="8a402-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a402-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="8a402-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="8a402-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="8a402-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="8a402-125">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="8a402-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="8a402-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="8a402-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a402-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="8a402-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="8a402-128">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="8a402-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8a402-129">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="8a402-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8a402-130">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="8a402-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8a402-131">Se ambas as interfaces forem implementadas, apenas os métodos de assincronia serão chamados.</span><span class="sxs-lookup"><span data-stu-id="8a402-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="8a402-132">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="8a402-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="8a402-133">Implementar filtros de página Razor globalmente</span><span class="sxs-lookup"><span data-stu-id="8a402-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="8a402-134">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a402-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="8a402-135">No código anterior, `ProcessUserAgent.Write` está o código fornecido pelo usuário que funciona com a seqüência de agentes do usuário.</span><span class="sxs-lookup"><span data-stu-id="8a402-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="8a402-136">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="8a402-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="8a402-137">As chamadas <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> de código `SampleAsyncPageFilter` a seguir para aplicar as somente páginas em */Filmes*:</span><span class="sxs-lookup"><span data-stu-id="8a402-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="8a402-138">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="8a402-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="8a402-139">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a402-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="8a402-140">Implementar filtros de página Razor substituindo os métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="8a402-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="8a402-141">O código a seguir substitui os filtros assíncronos da Página de Navalha:</span><span class="sxs-lookup"><span data-stu-id="8a402-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="8a402-142">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="8a402-142">Implement a filter attribute</span></span>

<span data-ttu-id="8a402-143">O filtro de filtro <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> integrado baseado em atributos pode ser subclassificado.</span><span class="sxs-lookup"><span data-stu-id="8a402-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="8a402-144">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="8a402-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="8a402-145">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="8a402-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="8a402-146">Use uma ferramenta como as ferramentas do desenvolvedor do navegador para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="8a402-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="8a402-147">Em **'Cabeçalhos de resposta',** `author: Rick` é exibido.</span><span class="sxs-lookup"><span data-stu-id="8a402-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="8a402-148">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="8a402-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="8a402-149">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="8a402-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="8a402-150">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="8a402-150">Authorize filter attribute</span></span>

<span data-ttu-id="8a402-151">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8a402-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a402-152">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a402-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8a402-153">Os filtros de página Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permitem que as Páginas Razor executem código antes e após a execução de um manipulador de Página Razor.</span><span class="sxs-lookup"><span data-stu-id="8a402-153">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="8a402-154">Os filtros de página Razor são semelhantes aos [filtros de ação de MVC do ASP.NET Core](xref:mvc/controllers/filters#action-filters), exceto que eles não podem ser aplicados aos métodos do manipulador de cada página individual.</span><span class="sxs-lookup"><span data-stu-id="8a402-154">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="8a402-155">Filtros de página Razor:</span><span class="sxs-lookup"><span data-stu-id="8a402-155">Razor Page filters:</span></span>

* <span data-ttu-id="8a402-156">Executam o código depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="8a402-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="8a402-157">Executam o código antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="8a402-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="8a402-158">Executam o código após a execução do método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="8a402-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="8a402-159">Podem ser implementados em uma única página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="8a402-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="8a402-160">Não podem ser aplicados a métodos do manipulador de uma página específica.</span><span class="sxs-lookup"><span data-stu-id="8a402-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="8a402-161">O código pode ser executado antes que um método do manipulador seja executado usando o construtor de página ou o middleware, mas somente os filtros de página Razor têm acesso a [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="8a402-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="8a402-162">Os filtros têm um parâmetro derivado [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), que fornece acesso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8a402-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="8a402-163">Por exemplo, a amostra [Implementar um atributo de filtro](#ifa) adiciona um cabeçalho à resposta, algo que não pode ser feito com construtores nem middlewares.</span><span class="sxs-lookup"><span data-stu-id="8a402-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="8a402-164">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a402-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a402-165">Os filtros de página Razor fornecem os métodos a seguir, que podem ser aplicados globalmente ou no nível da página:</span><span class="sxs-lookup"><span data-stu-id="8a402-165">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="8a402-166">Métodos síncronos:</span><span class="sxs-lookup"><span data-stu-id="8a402-166">Synchronous methods:</span></span>

  * <span data-ttu-id="8a402-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): chamado depois que um método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="8a402-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a402-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): chamado antes que o método do manipulador seja executado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="8a402-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="8a402-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): chamado depois que o método do manipulador é executado, antes do resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="8a402-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="8a402-170">Métodos assíncronos:</span><span class="sxs-lookup"><span data-stu-id="8a402-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="8a402-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): chamado de forma assíncrona depois que o método do manipulador é selecionado, mas antes que o model binding ocorra.</span><span class="sxs-lookup"><span data-stu-id="8a402-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="8a402-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): chamado de forma assíncrona, antes que o método do manipulador seja invocado, após a conclusão do model binding.</span><span class="sxs-lookup"><span data-stu-id="8a402-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="8a402-173">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, não ambas.</span><span class="sxs-lookup"><span data-stu-id="8a402-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="8a402-174">Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="8a402-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8a402-175">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="8a402-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8a402-176">Se ambas as interfaces forem implementadas, apenas os métodos de assincronia serão chamados.</span><span class="sxs-lookup"><span data-stu-id="8a402-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="8a402-177">A mesma regra aplica-se para substituições em páginas. Implemente a versão síncrona ou a assíncrona da substituição, não ambas.</span><span class="sxs-lookup"><span data-stu-id="8a402-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="8a402-178">Implementar filtros de página Razor globalmente</span><span class="sxs-lookup"><span data-stu-id="8a402-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="8a402-179">O código a seguir implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a402-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="8a402-180">No código anterior, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) não é necessário.</span><span class="sxs-lookup"><span data-stu-id="8a402-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="8a402-181">Ele é usado na amostra para fornecer informações de rastreamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a402-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="8a402-182">O código a seguir habilita o `SampleAsyncPageFilter` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="8a402-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="8a402-183">O código a seguir mostra a classe `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="8a402-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="8a402-184">O código a seguir chama `AddFolderApplicationModelConvention` para aplicar o `SampleAsyncPageFilter` somente às páginas em */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="8a402-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="8a402-185">O código a seguir implementa o `IPageFilter` síncrono:</span><span class="sxs-lookup"><span data-stu-id="8a402-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="8a402-186">O código a seguir habilita o `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="8a402-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="8a402-187">Implementar filtros de página Razor substituindo os métodos de filtro</span><span class="sxs-lookup"><span data-stu-id="8a402-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="8a402-188">O código a seguir substitui os filtros de página Razor síncronos:</span><span class="sxs-lookup"><span data-stu-id="8a402-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="8a402-189">Implementar um atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="8a402-189">Implement a filter attribute</span></span>

<span data-ttu-id="8a402-190">O filtro baseado em atributo interno [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) pode tornar-se uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="8a402-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="8a402-191">O filtro a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="8a402-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="8a402-192">O código a seguir se aplica ao atributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="8a402-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="8a402-193">Confira [Substituindo a ordem padrão](xref:mvc/controllers/filters#overriding-the-default-order) para obter instruções sobre a substituição da ordem.</span><span class="sxs-lookup"><span data-stu-id="8a402-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="8a402-194">Confira [Cancelamento e curto-circuito](xref:mvc/controllers/filters#cancellation-and-short-circuiting) para obter instruções para causar um curto-circuito no pipeline do filtro por meio de um filtro.</span><span class="sxs-lookup"><span data-stu-id="8a402-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="8a402-195">Autorizar o atributo de filtro</span><span class="sxs-lookup"><span data-stu-id="8a402-195">Authorize filter attribute</span></span>

<span data-ttu-id="8a402-196">O atributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) pode ser aplicado a um `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8a402-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
