---
title: Filtros no ASP.NET Core
author: Rick-Anderson
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 7fe33a620e43603388dd0cacb3ea42f5b5adc40f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408286"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="a16f7-103">Filtros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a16f7-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a16f7-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a16f7-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a16f7-105">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="a16f7-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a16f7-106">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="a16f7-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a16f7-107">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="a16f7-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a16f7-108">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="a16f7-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="a16f7-109">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a16f7-110">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="a16f7-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="a16f7-111">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="a16f7-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="a16f7-112">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a16f7-113">Este documento se aplica a Razor páginas, controladores de API e controladores com exibições.</span><span class="sxs-lookup"><span data-stu-id="a16f7-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="a16f7-114">Os filtros não funcionam diretamente com os [ Razor componentes](xref:blazor/components/index)do.</span><span class="sxs-lookup"><span data-stu-id="a16f7-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="a16f7-115">Um filtro só pode afetar indiretamente um componente quando:</span><span class="sxs-lookup"><span data-stu-id="a16f7-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="a16f7-116">O componente é inserido em uma página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="a16f7-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="a16f7-117">A página ou o controlador/modo de exibição usa o filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="a16f7-118">[Exibir ou baixar exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a16f7-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a16f7-119">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="a16f7-119">How filters work</span></span>

<span data-ttu-id="a16f7-120">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="a16f7-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="a16f7-121">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de outro middleware, middleware de roteamento, seleção de ação e o pipeline de invocação de ação.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a16f7-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-124">Filter types</span></span>

<span data-ttu-id="a16f7-125">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="a16f7-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="a16f7-126">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="a16f7-127">Os filtros de autorização de circuito curto do pipeline se a solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="a16f7-128">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="a16f7-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="a16f7-129">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-129">Run after authorization.</span></span>  
  * <span data-ttu-id="a16f7-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>executa o código antes do resto do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="a16f7-131">Por exemplo, `OnResourceExecuting` executa o código antes da Associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="a16f7-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>executa o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="a16f7-133">[Filtros de ação](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="a16f7-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="a16f7-134">Execute o código imediatamente antes e depois que um método de ação for chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="a16f7-135">Pode alterar os argumentos passados para uma ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="a16f7-136">Pode alterar o resultado retornado da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="a16f7-137">**Não** têm suporte em Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="a16f7-138">Os [filtros de exceção](#exception-filters) aplicam políticas globais a exceções sem tratamento que ocorrem antes da gravação do corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="a16f7-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="a16f7-139">Os [filtros de resultado](#result-filters) executam o código imediatamente antes e após a execução dos resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="a16f7-140">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="a16f7-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a16f7-141">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a16f7-142">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a16f7-145">Implementação</span><span class="sxs-lookup"><span data-stu-id="a16f7-145">Implementation</span></span>

<span data-ttu-id="a16f7-146">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="a16f7-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="a16f7-147">Os filtros síncronos executam o código antes e depois do estágio do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="a16f7-148">Por exemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="a16f7-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="a16f7-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a16f7-150">Os filtros assíncronos definem um `On-Stage-ExecutionAsync` método.</span><span class="sxs-lookup"><span data-stu-id="a16f7-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="a16f7-151">Por exemplo <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="a16f7-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="a16f7-152">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="a16f7-153">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-153">Multiple filter stages</span></span>

<span data-ttu-id="a16f7-154">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="a16f7-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="a16f7-155">Por exemplo, a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implementa:</span><span class="sxs-lookup"><span data-stu-id="a16f7-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="a16f7-156">Síncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="a16f7-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="a16f7-157">Assíncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a16f7-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="a16f7-158">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a16f7-159">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="a16f7-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a16f7-160">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="a16f7-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a16f7-161">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="a16f7-162">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a16f7-163">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="a16f7-163">Built-in filter attributes</span></span>

<span data-ttu-id="a16f7-164">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="a16f7-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="a16f7-165">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="a16f7-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="a16f7-166">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="a16f7-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="a16f7-167">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="a16f7-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="a16f7-168">Use uma ferramenta como as [ferramentas de desenvolvedor do navegador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="a16f7-169">Em **cabeçalhos de resposta**, `author: Rick Anderson` é exibido.</span><span class="sxs-lookup"><span data-stu-id="a16f7-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="a16f7-170">O código a seguir implementa um `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="a16f7-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="a16f7-171">Lê o título e o nome do sistema de configuração.</span><span class="sxs-lookup"><span data-stu-id="a16f7-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="a16f7-172">Ao contrário do exemplo anterior, o código a seguir não exige que os parâmetros de filtro sejam adicionados ao código.</span><span class="sxs-lookup"><span data-stu-id="a16f7-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="a16f7-173">Adiciona o título e o nome ao cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="a16f7-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a16f7-174">As opções de configuração são fornecidas no [sistema de configuração](xref:fundamentals/configuration/index) usando o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a16f7-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a16f7-175">Por exemplo, do *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="a16f7-176">No `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="a16f7-177">A `PositionOptions` classe é adicionada ao contêiner de serviço com a `"Position"` área de configuração.</span><span class="sxs-lookup"><span data-stu-id="a16f7-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="a16f7-178">O `MyActionFilterAttribute` é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="a16f7-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="a16f7-179">O código a seguir mostra a `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="a16f7-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a16f7-180">O código a seguir aplica o `MyActionFilterAttribute` ao `Index2` método:</span><span class="sxs-lookup"><span data-stu-id="a16f7-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="a16f7-181">Em **cabeçalhos de resposta**, `author: Rick Anderson` e `Editor: Joe Smith` é exibido quando o `Sample/Index2` ponto de extremidade é chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="a16f7-182">O código a seguir aplica o `MyActionFilterAttribute` e o `AddHeaderAttribute` à Razor página:</span><span class="sxs-lookup"><span data-stu-id="a16f7-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="a16f7-183">Os filtros não podem ser aplicados aos Razor métodos do manipulador de páginas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="a16f7-184">Eles podem ser aplicados ao modelo de Razor página ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="a16f7-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="a16f7-185">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a16f7-186">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="a16f7-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a16f7-187">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="a16f7-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="a16f7-188">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="a16f7-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="a16f7-189">Usando um atributo em uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="a16f7-190">Atributos de filtro não podem ser aplicados a Razor métodos de manipulador de páginas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="a16f7-191">Usando um atributo em um controlador ou uma Razor página.</span><span class="sxs-lookup"><span data-stu-id="a16f7-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="a16f7-192">Globalmente para todos os controladores, ações e Razor páginas, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a16f7-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="a16f7-193">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="a16f7-193">Default order of execution</span></span>

<span data-ttu-id="a16f7-194">Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a16f7-195">Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método.</span><span class="sxs-lookup"><span data-stu-id="a16f7-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="a16f7-196">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="a16f7-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a16f7-197">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="a16f7-197">The filter sequence:</span></span>

* <span data-ttu-id="a16f7-198">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="a16f7-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="a16f7-199">O código *anterior* de filtros de Razor página e controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="a16f7-200">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="a16f7-201">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="a16f7-202">O código *After* de filtros de Razor página e controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="a16f7-203">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="a16f7-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="a16f7-204">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="a16f7-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="a16f7-205">Sequência</span><span class="sxs-lookup"><span data-stu-id="a16f7-205">Sequence</span></span> | <span data-ttu-id="a16f7-206">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-206">Filter scope</span></span> | <span data-ttu-id="a16f7-207">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a16f7-208">1</span><span class="sxs-lookup"><span data-stu-id="a16f7-208">1</span></span> | <span data-ttu-id="a16f7-209">Global</span><span class="sxs-lookup"><span data-stu-id="a16f7-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a16f7-210">2</span><span class="sxs-lookup"><span data-stu-id="a16f7-210">2</span></span> | <span data-ttu-id="a16f7-211">Controlador ou Razor página</span><span class="sxs-lookup"><span data-stu-id="a16f7-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="a16f7-212">3</span><span class="sxs-lookup"><span data-stu-id="a16f7-212">3</span></span> | <span data-ttu-id="a16f7-213">Método</span><span class="sxs-lookup"><span data-stu-id="a16f7-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a16f7-214">4</span><span class="sxs-lookup"><span data-stu-id="a16f7-214">4</span></span> | <span data-ttu-id="a16f7-215">Método</span><span class="sxs-lookup"><span data-stu-id="a16f7-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a16f7-216">5</span><span class="sxs-lookup"><span data-stu-id="a16f7-216">5</span></span> | <span data-ttu-id="a16f7-217">Controlador ou Razor página</span><span class="sxs-lookup"><span data-stu-id="a16f7-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a16f7-218">6</span><span class="sxs-lookup"><span data-stu-id="a16f7-218">6</span></span> | <span data-ttu-id="a16f7-219">Global</span><span class="sxs-lookup"><span data-stu-id="a16f7-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="a16f7-220">Filtros de nível de controlador</span><span class="sxs-lookup"><span data-stu-id="a16f7-220">Controller level filters</span></span>

<span data-ttu-id="a16f7-221">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="a16f7-222">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="a16f7-222">These methods:</span></span>

* <span data-ttu-id="a16f7-223">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="a16f7-224">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="a16f7-225">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="a16f7-226">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="a16f7-227">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="a16f7-228">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="a16f7-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-229">The `TestController`:</span></span>

* <span data-ttu-id="a16f7-230">Aplica o `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à `FilterTest2` ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="a16f7-231">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="a16f7-232">Navegar até `https://localhost:5001/Test2/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a16f7-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="a16f7-233">Filtros de nível de controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="a16f7-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="a16f7-234">Os filtros de nível de controlador **não** podem ser definidos para execução após os filtros aplicados aos métodos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="a16f7-235">A ordem é explicada na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-235">Order is explained in the next section.</span></span>

<span data-ttu-id="a16f7-236">Para Razor páginas, consulte [implementar Razor filtros de página substituindo métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="a16f7-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a16f7-237">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="a16f7-237">Overriding the default order</span></span>

<span data-ttu-id="a16f7-238">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="a16f7-239">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="a16f7-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a16f7-240">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="a16f7-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="a16f7-241">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="a16f7-242">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="a16f7-243">A `Order` propriedade é definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="a16f7-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="a16f7-244">Considere os dois filtros de ação no controlador a seguir:</span><span class="sxs-lookup"><span data-stu-id="a16f7-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="a16f7-245">Um filtro global é adicionado em `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a16f7-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="a16f7-246">Os 3 filtros são executados na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="a16f7-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="a16f7-247">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="a16f7-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="a16f7-248">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="a16f7-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a16f7-249">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="a16f7-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a16f7-250">Conforme mencionado anteriormente, os filtros no nível do controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` para filtros internos, o escopo determina a ordem, a menos que `Order` esteja definido como um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="a16f7-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="a16f7-251">No código anterior, `MySampleActionFilter` tem escopo global para que ele seja executado antes `MyAction2FilterAttribute` , que tem o escopo do controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="a16f7-252">Para fazer a `MyAction2FilterAttribute` execução primeiro, defina a ordem como `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="a16f7-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="a16f7-253">Para fazer o filtro global `MySampleActionFilter` ser executado primeiro, defina `Order` como `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="a16f7-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a16f7-254">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="a16f7-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="a16f7-255">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="a16f7-256">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="a16f7-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a16f7-257">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a16f7-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a16f7-259">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a16f7-260">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a16f7-261">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a16f7-262">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a16f7-263">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="a16f7-264">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="a16f7-264">Dependency injection</span></span>

<span data-ttu-id="a16f7-265">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="a16f7-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a16f7-266">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="a16f7-267">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="a16f7-268">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="a16f7-268">A type-activated filter means:</span></span>

* <span data-ttu-id="a16f7-269">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-269">An instance is created for each request.</span></span>
* <span data-ttu-id="a16f7-270">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="a16f7-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="a16f7-271">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="a16f7-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a16f7-272">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="a16f7-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="a16f7-273">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="a16f7-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="a16f7-274">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a16f7-275">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="a16f7-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="a16f7-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="a16f7-277">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="a16f7-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="a16f7-278">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-278">Loggers are available from DI.</span></span> <span data-ttu-id="a16f7-279">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="a16f7-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="a16f7-280">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="a16f7-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="a16f7-281">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="a16f7-281">Logging added to filters:</span></span>

* <span data-ttu-id="a16f7-282">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="a16f7-283">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="a16f7-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="a16f7-284">Os filtros internos registram ações de log e eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="a16f7-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a16f7-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a16f7-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="a16f7-286">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="a16f7-287">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="a16f7-288">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a16f7-289">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="a16f7-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="a16f7-290">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="a16f7-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a16f7-291">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a16f7-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="a16f7-292">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a16f7-293">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="a16f7-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="a16f7-294">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="a16f7-295">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="a16f7-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="a16f7-296">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="a16f7-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="a16f7-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a16f7-298">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a16f7-299">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a16f7-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a16f7-300">TypeFilterAttribute</span></span>

<span data-ttu-id="a16f7-301">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a16f7-302">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="a16f7-303">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="a16f7-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="a16f7-304">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="a16f7-305">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="a16f7-306">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a16f7-307">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a16f7-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="a16f7-308">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a16f7-309">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="a16f7-310">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="a16f7-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a16f7-311">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="a16f7-312">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="a16f7-312">Authorization filters</span></span>

<span data-ttu-id="a16f7-313">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="a16f7-313">Authorization filters:</span></span>

* <span data-ttu-id="a16f7-314">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="a16f7-315">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-315">Control access to action methods.</span></span>
* <span data-ttu-id="a16f7-316">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="a16f7-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="a16f7-317">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="a16f7-318">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a16f7-319">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="a16f7-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="a16f7-320">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-320">Calls the authorization system.</span></span>
* <span data-ttu-id="a16f7-321">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="a16f7-321">Does not authorize requests.</span></span>

<span data-ttu-id="a16f7-322">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="a16f7-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="a16f7-323">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-323">The exception will not be handled.</span></span>
* <span data-ttu-id="a16f7-324">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="a16f7-325">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="a16f7-326">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="a16f7-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a16f7-327">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="a16f7-327">Resource filters</span></span>

<span data-ttu-id="a16f7-328">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="a16f7-328">Resource filters:</span></span>

* <span data-ttu-id="a16f7-329">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="a16f7-330">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="a16f7-331">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="a16f7-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="a16f7-332">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="a16f7-333">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="a16f7-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="a16f7-334">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="a16f7-334">Resource filter examples:</span></span>

* <span data-ttu-id="a16f7-335">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a16f7-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="a16f7-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="a16f7-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="a16f7-337">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="a16f7-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="a16f7-338">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="a16f7-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a16f7-339">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="a16f7-339">Action filters</span></span>

<span data-ttu-id="a16f7-340">Os filtros de ação **não** se aplicam a Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="a16f7-341">Páginas dão suporte a <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="a16f7-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a16f7-342">Para obter mais informações, consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a16f7-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a16f7-343">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-343">Action filters:</span></span>

* <span data-ttu-id="a16f7-344">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="a16f7-345">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a16f7-346">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a16f7-347">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="a16f7-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a16f7-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– permite ler as entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="a16f7-349"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="a16f7-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="a16f7-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="a16f7-351">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="a16f7-352">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="a16f7-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="a16f7-353">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="a16f7-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a16f7-354">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="a16f7-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a16f7-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a16f7-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="a16f7-357">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-357">Setting this property to null:</span></span>

  * <span data-ttu-id="a16f7-358">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="a16f7-359">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="a16f7-360">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="a16f7-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="a16f7-361">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a16f7-362">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="a16f7-363">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="a16f7-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="a16f7-364">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="a16f7-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="a16f7-365">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="a16f7-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="a16f7-366">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-366">Validate model state.</span></span>
* <span data-ttu-id="a16f7-367">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="a16f7-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="a16f7-368">Os controladores anotados com o `[ApiController]` atributo validam automaticamente o estado do modelo e retornam uma resposta de 400.</span><span class="sxs-lookup"><span data-stu-id="a16f7-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="a16f7-369">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="a16f7-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="a16f7-370">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="a16f7-371">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="a16f7-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a16f7-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a16f7-374">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="a16f7-375">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="a16f7-376">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="a16f7-377">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="a16f7-377">Exception filters</span></span>

<span data-ttu-id="a16f7-378">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-378">Exception filters:</span></span>

* <span data-ttu-id="a16f7-379">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="a16f7-380">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="a16f7-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="a16f7-381">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="a16f7-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="a16f7-382">O código a seguir testa o filtro de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="a16f7-383">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-383">Exception filters:</span></span>

* <span data-ttu-id="a16f7-384">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="a16f7-384">Don't have before and after events.</span></span>
* <span data-ttu-id="a16f7-385">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="a16f7-386">Manipule exceções sem tratamento que ocorrem em Razor criação de página ou controlador, [Associação de modelo](xref:mvc/models/model-binding), filtros de ação ou métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="a16f7-387">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="a16f7-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="a16f7-388">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="a16f7-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="a16f7-389">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-389">This stops propagation of the exception.</span></span> <span data-ttu-id="a16f7-390">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="a16f7-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a16f7-391">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="a16f7-391">Only an action filter can do that.</span></span>

<span data-ttu-id="a16f7-392">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-392">Exception filters:</span></span>

* <span data-ttu-id="a16f7-393">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="a16f7-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="a16f7-394">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="a16f7-395">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a16f7-396">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="a16f7-397">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="a16f7-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a16f7-398">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="a16f7-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="a16f7-399">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="a16f7-399">Result filters</span></span>

<span data-ttu-id="a16f7-400">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="a16f7-400">Result filters:</span></span>

* <span data-ttu-id="a16f7-401">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="a16f7-401">Implement an interface:</span></span>
  * <span data-ttu-id="a16f7-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a16f7-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="a16f7-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="a16f7-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="a16f7-404">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a16f7-405">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a16f7-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a16f7-406">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="a16f7-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a16f7-407">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="a16f7-408">Uma ação que retorna uma exibição inclui todo o processamento do Razor como parte da <xref:Microsoft.AspNetCore.Mvc.ViewResult> execução.</span><span class="sxs-lookup"><span data-stu-id="a16f7-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="a16f7-409">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a16f7-410">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="a16f7-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="a16f7-411">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="a16f7-412">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="a16f7-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="a16f7-413">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="a16f7-414">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="a16f7-415">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="a16f7-416">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="a16f7-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a16f7-417">Gerando uma exceção em `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="a16f7-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="a16f7-418">Impede a execução do resultado da ação e dos filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="a16f7-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a16f7-419">É tratado como uma falha em vez de um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="a16f7-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a16f7-420">Quando o <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> método é executado, a resposta provavelmente já foi enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="a16f7-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="a16f7-421">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="a16f7-422">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a16f7-423">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a16f7-424">Definir `Exception` como NULL manipula uma exceção e impede que a exceção seja gerada novamente mais tarde no pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="a16f7-425">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="a16f7-426">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="a16f7-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a16f7-427">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a16f7-428">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="a16f7-429">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="a16f7-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="a16f7-430">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a16f7-431">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a16f7-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a16f7-432">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="a16f7-433">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="a16f7-433">This includes action results produced by:</span></span>

* <span data-ttu-id="a16f7-434">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="a16f7-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="a16f7-435">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-435">Exception filters.</span></span>

<span data-ttu-id="a16f7-436">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="a16f7-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a16f7-437">IFilterFactory</span></span>

<span data-ttu-id="a16f7-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a16f7-439">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a16f7-440">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a16f7-441">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="a16f7-442">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a16f7-443">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="a16f7-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="a16f7-444">O filtro é aplicado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a16f7-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="a16f7-445">Teste o código anterior executando o [exemplo de download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="a16f7-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="a16f7-446">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="a16f7-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="a16f7-447">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="a16f7-448">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="a16f7-449">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="a16f7-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="a16f7-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="a16f7-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="a16f7-451">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="a16f7-451">**internal:** `My header`</span></span>

<span data-ttu-id="a16f7-452">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="a16f7-453">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="a16f7-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="a16f7-454">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="a16f7-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="a16f7-455">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="a16f7-456">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a16f7-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a16f7-458">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a16f7-459">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="a16f7-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a16f7-460">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a16f7-461">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a16f7-462">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="a16f7-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a16f7-463">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a16f7-464">Mas os filtros diferem do middleware, pois fazem parte do tempo de execução, o que significa que eles têm acesso ao contexto e às construções.</span><span class="sxs-lookup"><span data-stu-id="a16f7-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="a16f7-465">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="a16f7-466">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="a16f7-467">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="a16f7-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a16f7-468">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a16f7-469">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="a16f7-469">Next actions</span></span>

* <span data-ttu-id="a16f7-470">Consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a16f7-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="a16f7-471">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="a16f7-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a16f7-472">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a16f7-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a16f7-473">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="a16f7-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a16f7-474">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="a16f7-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a16f7-475">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="a16f7-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a16f7-476">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="a16f7-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="a16f7-477">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a16f7-478">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="a16f7-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="a16f7-479">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="a16f7-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="a16f7-480">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a16f7-481">Este documento se aplica a Razor páginas, controladores de API e controladores com exibições.</span><span class="sxs-lookup"><span data-stu-id="a16f7-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="a16f7-482">[Exibir ou baixar exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a16f7-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a16f7-483">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="a16f7-483">How filters work</span></span>

<span data-ttu-id="a16f7-484">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="a16f7-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="a16f7-485">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a16f7-488">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-488">Filter types</span></span>

<span data-ttu-id="a16f7-489">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="a16f7-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="a16f7-490">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="a16f7-491">Os filtros de autorização podem causar um curto-circuito do pipeline se uma solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="a16f7-492">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="a16f7-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="a16f7-493">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-493">Run after authorization.</span></span>  
  * <span data-ttu-id="a16f7-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> pode executar o código antes do restante do pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="a16f7-495">Por exemplo, `OnResourceExecuting` pode executar o código antes do model binding.</span><span class="sxs-lookup"><span data-stu-id="a16f7-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="a16f7-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> pode executar o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="a16f7-497">[Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="a16f7-498">Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="a16f7-499">**Não** há suporte para filtros de ação em Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="a16f7-500">[Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="a16f7-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="a16f7-501">[Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais.</span><span class="sxs-lookup"><span data-stu-id="a16f7-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="a16f7-502">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="a16f7-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a16f7-503">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a16f7-504">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a16f7-507">Implementação</span><span class="sxs-lookup"><span data-stu-id="a16f7-507">Implementation</span></span>

<span data-ttu-id="a16f7-508">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="a16f7-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="a16f7-509">Os filtros síncronos podem executar o código antes (`On-Stage-Executing`) e depois (`On-Stage-Executed`) do estágio de pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="a16f7-510">Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="a16f7-511">`OnActionExecuted` é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="a16f7-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a16f7-512">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="a16f7-513">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="a16f7-514">Cada um dos métodos `On-Stage-ExecutionAsync` usa um `FilterType-ExecutionDelegate` que executa o estágio de pipeline do filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="a16f7-515">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-515">Multiple filter stages</span></span>

<span data-ttu-id="a16f7-516">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="a16f7-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="a16f7-517">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="a16f7-518">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a16f7-519">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="a16f7-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a16f7-520">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="a16f7-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a16f7-521">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="a16f7-522">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a16f7-523">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="a16f7-523">Built-in filter attributes</span></span>

<span data-ttu-id="a16f7-524">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="a16f7-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="a16f7-525">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="a16f7-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="a16f7-526">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="a16f7-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="a16f7-527">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="a16f7-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="a16f7-528">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a16f7-529">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="a16f7-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a16f7-530">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="a16f7-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="a16f7-531">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="a16f7-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="a16f7-532">Usando um atributo em uma ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="a16f7-533">Usando um atributo em um controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="a16f7-534">Globalmente para todos os controladores e ações, como mostra o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a16f7-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a16f7-535">O código anterior adiciona três filtros globalmente usando a coleção [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="a16f7-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="a16f7-536">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="a16f7-536">Default order of execution</span></span>

<span data-ttu-id="a16f7-537">Quando há vários filtros *do mesmo tipo*, o escopo determina a ordem padrão de execução do filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a16f7-538">Filtros globais envolvem filtros de classe.</span><span class="sxs-lookup"><span data-stu-id="a16f7-538">Global filters surround class filters.</span></span> <span data-ttu-id="a16f7-539">Filtros de classe envolvem filtros de método.</span><span class="sxs-lookup"><span data-stu-id="a16f7-539">Class filters surround method filters.</span></span>

<span data-ttu-id="a16f7-540">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="a16f7-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a16f7-541">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="a16f7-541">The filter sequence:</span></span>

* <span data-ttu-id="a16f7-542">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="a16f7-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="a16f7-543">O código *anterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="a16f7-544">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="a16f7-545">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="a16f7-546">O código *posterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="a16f7-547">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="a16f7-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="a16f7-548">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="a16f7-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="a16f7-549">Sequência</span><span class="sxs-lookup"><span data-stu-id="a16f7-549">Sequence</span></span> | <span data-ttu-id="a16f7-550">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-550">Filter scope</span></span> | <span data-ttu-id="a16f7-551">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a16f7-552">1</span><span class="sxs-lookup"><span data-stu-id="a16f7-552">1</span></span> | <span data-ttu-id="a16f7-553">Global</span><span class="sxs-lookup"><span data-stu-id="a16f7-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a16f7-554">2</span><span class="sxs-lookup"><span data-stu-id="a16f7-554">2</span></span> | <span data-ttu-id="a16f7-555">Controller</span><span class="sxs-lookup"><span data-stu-id="a16f7-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a16f7-556">3</span><span class="sxs-lookup"><span data-stu-id="a16f7-556">3</span></span> | <span data-ttu-id="a16f7-557">Método</span><span class="sxs-lookup"><span data-stu-id="a16f7-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a16f7-558">4</span><span class="sxs-lookup"><span data-stu-id="a16f7-558">4</span></span> | <span data-ttu-id="a16f7-559">Método</span><span class="sxs-lookup"><span data-stu-id="a16f7-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a16f7-560">5</span><span class="sxs-lookup"><span data-stu-id="a16f7-560">5</span></span> | <span data-ttu-id="a16f7-561">Controller</span><span class="sxs-lookup"><span data-stu-id="a16f7-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a16f7-562">6</span><span class="sxs-lookup"><span data-stu-id="a16f7-562">6</span></span> | <span data-ttu-id="a16f7-563">Global</span><span class="sxs-lookup"><span data-stu-id="a16f7-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="a16f7-564">Esta sequência mostra:</span><span class="sxs-lookup"><span data-stu-id="a16f7-564">This sequence shows:</span></span>

* <span data-ttu-id="a16f7-565">O filtro de método está aninhado no filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="a16f7-566">O filtro de controlador está aninhado no filtro global.</span><span class="sxs-lookup"><span data-stu-id="a16f7-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="a16f7-567">Filtros de nível de página e de controlador Razor</span><span class="sxs-lookup"><span data-stu-id="a16f7-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="a16f7-568">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="a16f7-569">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="a16f7-569">These methods:</span></span>

* <span data-ttu-id="a16f7-570">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="a16f7-571">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="a16f7-572">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="a16f7-573">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="a16f7-574">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="a16f7-575">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="a16f7-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-576">The `TestController`:</span></span>

* <span data-ttu-id="a16f7-577">Aplica o `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à `FilterTest2` ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="a16f7-578">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="a16f7-579">Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a16f7-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="a16f7-580">Para Razor páginas, consulte [implementar Razor filtros de página substituindo métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="a16f7-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a16f7-581">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="a16f7-581">Overriding the default order</span></span>

<span data-ttu-id="a16f7-582">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="a16f7-583">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="a16f7-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a16f7-584">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="a16f7-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="a16f7-585">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="a16f7-586">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="a16f7-587">A propriedade `Order` pode ser definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="a16f7-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="a16f7-588">Considere os mesmos filtros de 3 ações mostrados no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="a16f7-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="a16f7-589">Se a propriedade `Order` do controlador e os filtros globais estiverem definidos como 1 e 2, respectivamente, a ordem de execução será invertida.</span><span class="sxs-lookup"><span data-stu-id="a16f7-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="a16f7-590">Sequência</span><span class="sxs-lookup"><span data-stu-id="a16f7-590">Sequence</span></span> | <span data-ttu-id="a16f7-591">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-591">Filter scope</span></span> | <span data-ttu-id="a16f7-592">Propriedade `Order`</span><span class="sxs-lookup"><span data-stu-id="a16f7-592">`Order` property</span></span> | <span data-ttu-id="a16f7-593">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="a16f7-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="a16f7-594">1</span><span class="sxs-lookup"><span data-stu-id="a16f7-594">1</span></span> | <span data-ttu-id="a16f7-595">Método</span><span class="sxs-lookup"><span data-stu-id="a16f7-595">Method</span></span> | <span data-ttu-id="a16f7-596">0</span><span class="sxs-lookup"><span data-stu-id="a16f7-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a16f7-597">2</span><span class="sxs-lookup"><span data-stu-id="a16f7-597">2</span></span> | <span data-ttu-id="a16f7-598">Controller</span><span class="sxs-lookup"><span data-stu-id="a16f7-598">Controller</span></span> | <span data-ttu-id="a16f7-599">1</span><span class="sxs-lookup"><span data-stu-id="a16f7-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a16f7-600">3</span><span class="sxs-lookup"><span data-stu-id="a16f7-600">3</span></span> | <span data-ttu-id="a16f7-601">Global</span><span class="sxs-lookup"><span data-stu-id="a16f7-601">Global</span></span> | <span data-ttu-id="a16f7-602">2</span><span class="sxs-lookup"><span data-stu-id="a16f7-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a16f7-603">4</span><span class="sxs-lookup"><span data-stu-id="a16f7-603">4</span></span> | <span data-ttu-id="a16f7-604">Global</span><span class="sxs-lookup"><span data-stu-id="a16f7-604">Global</span></span> | <span data-ttu-id="a16f7-605">2</span><span class="sxs-lookup"><span data-stu-id="a16f7-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a16f7-606">5</span><span class="sxs-lookup"><span data-stu-id="a16f7-606">5</span></span> | <span data-ttu-id="a16f7-607">Controller</span><span class="sxs-lookup"><span data-stu-id="a16f7-607">Controller</span></span> | <span data-ttu-id="a16f7-608">1</span><span class="sxs-lookup"><span data-stu-id="a16f7-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a16f7-609">6</span><span class="sxs-lookup"><span data-stu-id="a16f7-609">6</span></span> | <span data-ttu-id="a16f7-610">Método</span><span class="sxs-lookup"><span data-stu-id="a16f7-610">Method</span></span> | <span data-ttu-id="a16f7-611">0</span><span class="sxs-lookup"><span data-stu-id="a16f7-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="a16f7-612">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="a16f7-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="a16f7-613">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="a16f7-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a16f7-614">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="a16f7-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a16f7-615">Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="a16f7-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a16f7-616">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="a16f7-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="a16f7-617">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="a16f7-618">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="a16f7-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a16f7-619">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a16f7-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a16f7-621">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a16f7-622">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a16f7-623">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a16f7-624">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a16f7-625">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="a16f7-626">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="a16f7-626">Dependency injection</span></span>

<span data-ttu-id="a16f7-627">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="a16f7-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a16f7-628">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="a16f7-629">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="a16f7-630">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="a16f7-630">A type-activated filter means:</span></span>

* <span data-ttu-id="a16f7-631">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-631">An instance is created for each request.</span></span>
* <span data-ttu-id="a16f7-632">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="a16f7-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="a16f7-633">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="a16f7-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a16f7-634">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="a16f7-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="a16f7-635">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="a16f7-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="a16f7-636">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="a16f7-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a16f7-637">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="a16f7-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="a16f7-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="a16f7-639">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="a16f7-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="a16f7-640">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-640">Loggers are available from DI.</span></span> <span data-ttu-id="a16f7-641">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="a16f7-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="a16f7-642">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="a16f7-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="a16f7-643">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="a16f7-643">Logging added to filters:</span></span>

* <span data-ttu-id="a16f7-644">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="a16f7-645">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="a16f7-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="a16f7-646">Os filtros internos registram ações e eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="a16f7-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a16f7-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a16f7-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="a16f7-648">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="a16f7-649">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="a16f7-650">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a16f7-651">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="a16f7-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a16f7-652">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="a16f7-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a16f7-653">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a16f7-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="a16f7-654">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a16f7-655">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="a16f7-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="a16f7-656">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="a16f7-657">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="a16f7-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="a16f7-658">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="a16f7-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="a16f7-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a16f7-660">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a16f7-661">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a16f7-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a16f7-662">TypeFilterAttribute</span></span>

<span data-ttu-id="a16f7-663">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a16f7-664">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="a16f7-665">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="a16f7-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="a16f7-666">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="a16f7-667">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="a16f7-668">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a16f7-669">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a16f7-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="a16f7-670">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a16f7-671">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="a16f7-672">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="a16f7-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a16f7-673">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="a16f7-674">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="a16f7-674">Authorization filters</span></span>

<span data-ttu-id="a16f7-675">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="a16f7-675">Authorization filters:</span></span>

* <span data-ttu-id="a16f7-676">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="a16f7-677">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-677">Control access to action methods.</span></span>
* <span data-ttu-id="a16f7-678">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="a16f7-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="a16f7-679">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="a16f7-680">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a16f7-681">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="a16f7-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="a16f7-682">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-682">Calls the authorization system.</span></span>
* <span data-ttu-id="a16f7-683">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="a16f7-683">Does not authorize requests.</span></span>

<span data-ttu-id="a16f7-684">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="a16f7-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="a16f7-685">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-685">The exception will not be handled.</span></span>
* <span data-ttu-id="a16f7-686">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="a16f7-687">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="a16f7-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="a16f7-688">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="a16f7-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a16f7-689">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="a16f7-689">Resource filters</span></span>

<span data-ttu-id="a16f7-690">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="a16f7-690">Resource filters:</span></span>

* <span data-ttu-id="a16f7-691">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="a16f7-692">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="a16f7-693">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="a16f7-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="a16f7-694">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="a16f7-695">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="a16f7-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="a16f7-696">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="a16f7-696">Resource filter examples:</span></span>

* <span data-ttu-id="a16f7-697">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a16f7-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="a16f7-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="a16f7-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="a16f7-699">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="a16f7-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="a16f7-700">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="a16f7-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a16f7-701">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="a16f7-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a16f7-702">Os filtros de ação **não** se aplicam a Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="a16f7-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="a16f7-703">Páginas dão suporte a <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="a16f7-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a16f7-704">Para obter mais informações, consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a16f7-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a16f7-705">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-705">Action filters:</span></span>

* <span data-ttu-id="a16f7-706">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="a16f7-707">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a16f7-708">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a16f7-709">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="a16f7-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a16f7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - permite a leitura das entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="a16f7-711"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="a16f7-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="a16f7-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="a16f7-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="a16f7-713">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="a16f7-714">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="a16f7-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="a16f7-715">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="a16f7-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a16f7-716">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="a16f7-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a16f7-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a16f7-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="a16f7-719">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-719">Setting this property to null:</span></span>

  * <span data-ttu-id="a16f7-720">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="a16f7-721">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="a16f7-722">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="a16f7-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="a16f7-723">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a16f7-724">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="a16f7-725">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="a16f7-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="a16f7-726">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="a16f7-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="a16f7-727">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="a16f7-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="a16f7-728">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="a16f7-728">Validate model state.</span></span>
* <span data-ttu-id="a16f7-729">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="a16f7-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="a16f7-730">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="a16f7-731">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="a16f7-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a16f7-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a16f7-734">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="a16f7-735">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="a16f7-736">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="a16f7-737">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="a16f7-737">Exception filters</span></span>

<span data-ttu-id="a16f7-738">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-738">Exception filters:</span></span>

* <span data-ttu-id="a16f7-739">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="a16f7-740">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="a16f7-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="a16f7-741">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="a16f7-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="a16f7-742">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-742">Exception filters:</span></span>

* <span data-ttu-id="a16f7-743">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="a16f7-743">Don't have before and after events.</span></span>
* <span data-ttu-id="a16f7-744">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="a16f7-745">Manipule exceções sem tratamento que ocorrem em Razor criação de página ou controlador, [Associação de modelo](xref:mvc/models/model-binding), filtros de ação ou métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="a16f7-746">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="a16f7-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="a16f7-747">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="a16f7-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="a16f7-748">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-748">This stops propagation of the exception.</span></span> <span data-ttu-id="a16f7-749">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="a16f7-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a16f7-750">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="a16f7-750">Only an action filter can do that.</span></span>

<span data-ttu-id="a16f7-751">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="a16f7-751">Exception filters:</span></span>

* <span data-ttu-id="a16f7-752">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="a16f7-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="a16f7-753">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="a16f7-754">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a16f7-755">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="a16f7-756">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="a16f7-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a16f7-757">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="a16f7-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="a16f7-758">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="a16f7-758">Result filters</span></span>

<span data-ttu-id="a16f7-759">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="a16f7-759">Result filters:</span></span>

* <span data-ttu-id="a16f7-760">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="a16f7-760">Implement an interface:</span></span>
  * <span data-ttu-id="a16f7-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a16f7-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="a16f7-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="a16f7-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="a16f7-763">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a16f7-764">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a16f7-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a16f7-765">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="a16f7-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a16f7-766">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="a16f7-767">Uma ação que retorna um modo de exibição incluiria todo o processamento de Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> em execução.</span><span class="sxs-lookup"><span data-stu-id="a16f7-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="a16f7-768">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a16f7-769">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="a16f7-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="a16f7-770">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="a16f7-771">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="a16f7-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="a16f7-772">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="a16f7-773">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="a16f7-774">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="a16f7-775">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="a16f7-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a16f7-776">A geração de uma exceção em `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="a16f7-777">Impedir a execução do resultado da ação e dos próximos filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a16f7-778">Ser tratada como uma falha e não como um resultado com êxito.</span><span class="sxs-lookup"><span data-stu-id="a16f7-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a16f7-779">Quando o <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> método é executado, a resposta provavelmente já foi enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="a16f7-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="a16f7-780">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada ainda mais.</span><span class="sxs-lookup"><span data-stu-id="a16f7-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="a16f7-781">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a16f7-782">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a16f7-783">Definir `Exception` como nulo trata uma exceção com eficiência e impede que ela seja gerada novamente pelo ASP.NET Core posteriormente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="a16f7-784">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="a16f7-785">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="a16f7-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a16f7-786">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a16f7-787">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="a16f7-788">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="a16f7-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="a16f7-789">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a16f7-790">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a16f7-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a16f7-791">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="a16f7-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="a16f7-792">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="a16f7-792">This includes action results produced by:</span></span>

* <span data-ttu-id="a16f7-793">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="a16f7-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="a16f7-794">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="a16f7-794">Exception filters.</span></span>

<span data-ttu-id="a16f7-795">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="a16f7-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a16f7-796">IFilterFactory</span></span>

<span data-ttu-id="a16f7-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a16f7-798">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="a16f7-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a16f7-799">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a16f7-800">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="a16f7-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="a16f7-801">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="a16f7-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a16f7-802">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="a16f7-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="a16f7-803">O código anterior pode ser testado executando o [exemplo para download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="a16f7-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="a16f7-804">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="a16f7-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="a16f7-805">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="a16f7-806">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="a16f7-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="a16f7-807">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="a16f7-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="a16f7-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="a16f7-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="a16f7-809">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="a16f7-809">**internal:** `My header`</span></span>

<span data-ttu-id="a16f7-810">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="a16f7-811">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="a16f7-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="a16f7-812">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="a16f7-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="a16f7-813">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="a16f7-814">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="a16f7-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a16f7-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a16f7-816">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="a16f7-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a16f7-817">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="a16f7-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a16f7-818">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="a16f7-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a16f7-819">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="a16f7-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a16f7-820">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="a16f7-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a16f7-821">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a16f7-822">Mas os filtros diferem do middleware porque fazem parte do ASP.NET Core, o que significa que têm acesso ao contexto e a constructos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a16f7-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="a16f7-823">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="a16f7-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="a16f7-824">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="a16f7-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="a16f7-825">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="a16f7-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a16f7-826">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="a16f7-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a16f7-827">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="a16f7-827">Next actions</span></span>

* <span data-ttu-id="a16f7-828">Consulte [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a16f7-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="a16f7-829">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="a16f7-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
