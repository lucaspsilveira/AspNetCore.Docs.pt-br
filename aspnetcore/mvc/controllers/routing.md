---
title: Roteamento para ações do controlador no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core MVC usa o middleware de roteamento para corresponder a URLs das solicitações de entrada e mapeá-las para ações.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: 4208ef8fb7a9b10621f214f79679ff8d7fd83996
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775018"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="2393d-103">Roteamento para ações do controlador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2393d-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="2393d-104">Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2393d-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2393d-105">Os controladores de ASP.NET Core usam o [middleware](xref:fundamentals/middleware/index) de roteamento para corresponder as URLs de solicitações de entrada e mapeá-las para [ações](#action).</span><span class="sxs-lookup"><span data-stu-id="2393d-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="2393d-106">Modelos de rotas:</span><span class="sxs-lookup"><span data-stu-id="2393d-106">Routes templates:</span></span>

* <span data-ttu-id="2393d-107">São definidos em código ou atributos de inicialização.</span><span class="sxs-lookup"><span data-stu-id="2393d-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="2393d-108">Descreva como os caminhos de URL são correspondidos às [ações](#action).</span><span class="sxs-lookup"><span data-stu-id="2393d-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="2393d-109">São usados para gerar URLs para links.</span><span class="sxs-lookup"><span data-stu-id="2393d-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="2393d-110">Os links gerados são normalmente retornados em respostas.</span><span class="sxs-lookup"><span data-stu-id="2393d-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="2393d-111">As ações são [roteadas por convenção](#cr) ou [roteadas por atributo](#ar).</span><span class="sxs-lookup"><span data-stu-id="2393d-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="2393d-112">Colocar uma rota no controlador ou na [ação](#action) a torna roteada por atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="2393d-113">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="2393d-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="2393d-114">Este documento:</span><span class="sxs-lookup"><span data-stu-id="2393d-114">This document:</span></span>

* <span data-ttu-id="2393d-115">Explica as interações entre o MVC e o roteamento:</span><span class="sxs-lookup"><span data-stu-id="2393d-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="2393d-116">Como os aplicativos MVC típicos fazem uso dos recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="2393d-117">Aborda ambos:</span><span class="sxs-lookup"><span data-stu-id="2393d-117">Covers both:</span></span>
    * <span data-ttu-id="2393d-118">[Roteamento convencional](#cr) normalmente usado com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="2393d-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="2393d-119">*Roteamento de atributos* usado com APIs REST.</span><span class="sxs-lookup"><span data-stu-id="2393d-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="2393d-120">Se você estiver interessado principalmente no roteamento de APIs REST, vá para a seção [Roteamento de atributo para APIs REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="2393d-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="2393d-121">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes avançados de roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="2393d-122">Refere-se ao sistema de roteamento padrão adicionado no ASP.NET Core 3,0, chamado roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="2393d-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="2393d-123">É possível usar controladores com a versão anterior do roteamento para fins de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="2393d-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="2393d-124">Consulte o [Guia de migração 2.2-3.0](xref:migration/22-to-30) para obter instruções.</span><span class="sxs-lookup"><span data-stu-id="2393d-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="2393d-125">Consulte a [versão 2,2 deste documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obter material de referência sobre o sistema de roteamento herdado.</span><span class="sxs-lookup"><span data-stu-id="2393d-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="2393d-126">Configurar a rota convencional</span><span class="sxs-lookup"><span data-stu-id="2393d-126">Set up conventional route</span></span>

<span data-ttu-id="2393d-127">`Startup.Configure`geralmente tem um código semelhante ao seguinte ao usar o [Roteamento convencional](#crd):</span><span class="sxs-lookup"><span data-stu-id="2393d-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="2393d-128">Dentro da chamada para <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> é usado para criar uma única rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="2393d-129">A rota única é chamada `default` rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-129">The single route is named `default` route.</span></span> <span data-ttu-id="2393d-130">A maioria dos aplicativos com controladores e exibições usa um modelo de `default` rota semelhante à rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="2393d-131">As APIs REST devem usar o [Roteamento de atributos](#ar).</span><span class="sxs-lookup"><span data-stu-id="2393d-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="2393d-132">O modelo `"{controller=Home}/{action=Index}/{id?}"`de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="2393d-133">Corresponde a um caminho de URL como`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="2393d-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="2393d-134">Extrai os valores `{ controller = Products, action = Details, id = 5 }` de rota por tokening do caminho.</span><span class="sxs-lookup"><span data-stu-id="2393d-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="2393d-135">A extração dos valores de rota resultará em uma correspondência se o aplicativo tiver `ProductsController` um controlador `Details` chamado e uma ação:</span><span class="sxs-lookup"><span data-stu-id="2393d-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="2393d-136">`/Products/Details/5`o modelo associa o valor de `id = 5` para definir o `id` parâmetro como `5`.</span><span class="sxs-lookup"><span data-stu-id="2393d-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="2393d-137">Consulte [Associação de modelo](xref:mvc/models/model-binding) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="2393d-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="2393d-138">`{controller=Home}`define `Home` como o padrão `controller`.</span><span class="sxs-lookup"><span data-stu-id="2393d-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="2393d-139">`{action=Index}`define `Index` como o padrão `action`.</span><span class="sxs-lookup"><span data-stu-id="2393d-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="2393d-140">O `?` caractere em `{id?}` define `id` como opcional.</span><span class="sxs-lookup"><span data-stu-id="2393d-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="2393d-141">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="2393d-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="2393d-142">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="2393d-143">Corresponde ao caminho `/`da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="2393d-144">Produz os valores `{ controller = Home, action = Index }`de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="2393d-145">Os valores para `controller` e `action` fazem uso dos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="2393d-146">`id`não produz um valor, pois não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="2393d-147">`/`corresponde somente se existir uma `HomeController` ação e `Index` :</span><span class="sxs-lookup"><span data-stu-id="2393d-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="2393d-148">Usando a definição de controlador e o modelo de rota `HomeController.Index` anteriores, a ação é executada para os seguintes caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="2393d-149">O caminho `/` da URL usa a ação e `Home` `Index` os controladores padrão do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="2393d-150">O caminho `/Home` da URL usa a ação padrão `Index` do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="2393d-151">O método de conveniência <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="2393d-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="2393d-152">Substituto</span><span class="sxs-lookup"><span data-stu-id="2393d-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="2393d-153">O roteamento é configurado usando <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> o <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> e o middleware.</span><span class="sxs-lookup"><span data-stu-id="2393d-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="2393d-154">Para usar controladores:</span><span class="sxs-lookup"><span data-stu-id="2393d-154">To use controllers:</span></span>
>
> * <span data-ttu-id="2393d-155">Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> dentro `UseEndpoints` para mapear controladores [roteados de atributo](#ar) .</span><span class="sxs-lookup"><span data-stu-id="2393d-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="2393d-156">Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, para mapear controladores [roteados de Convenção](#cr) .</span><span class="sxs-lookup"><span data-stu-id="2393d-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="2393d-157">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="2393d-157">Conventional routing</span></span>

<span data-ttu-id="2393d-158">O roteamento convencional é usado com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="2393d-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="2393d-159">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="2393d-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="2393d-160">é um exemplo de *roteamento convencional*.</span><span class="sxs-lookup"><span data-stu-id="2393d-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="2393d-161">Ele é chamado de *Roteamento convencional* porque estabelece uma *Convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="2393d-162">O primeiro segmento de caminho `{controller=Home}`,, é mapeado para o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="2393d-163">O segundo segmento, `{action=Index}`, é mapeado para o nome da [ação](#action) .</span><span class="sxs-lookup"><span data-stu-id="2393d-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="2393d-164">O terceiro segmento `{id?}` é usado para um opcional `id`.</span><span class="sxs-lookup"><span data-stu-id="2393d-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="2393d-165">O `?` no `{id?}` torna opcional.</span><span class="sxs-lookup"><span data-stu-id="2393d-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="2393d-166">`id`é usado para mapear para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="2393d-167">Usando essa `default` rota, o caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="2393d-168">`/Products/List`mapeia para a `ProductsController.List` ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="2393d-169">`/Blog/Article/17`mapeia para `BlogController.Article` e normalmente vincula o `id` parâmetro a 17.</span><span class="sxs-lookup"><span data-stu-id="2393d-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="2393d-170">Este mapeamento:</span><span class="sxs-lookup"><span data-stu-id="2393d-170">This mapping:</span></span>

* <span data-ttu-id="2393d-171">É baseado **somente**nos nomes de controlador e de [ação](#action) .</span><span class="sxs-lookup"><span data-stu-id="2393d-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="2393d-172">Não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="2393d-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="2393d-173">O uso do roteamento convencional com a rota padrão permite a criação do aplicativo sem a necessidade de surgir um novo padrão de URL para cada ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="2393d-174">Para um aplicativo com ações de estilo [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , tendo consistência para as URLs entre controladores:</span><span class="sxs-lookup"><span data-stu-id="2393d-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="2393d-175">Ajuda a simplificar o código.</span><span class="sxs-lookup"><span data-stu-id="2393d-175">Helps simplify the code.</span></span>
* <span data-ttu-id="2393d-176">Torna a interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="2393d-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="2393d-177">O `id` no código anterior é definido como opcional pelo modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="2393d-178">As ações podem ser executadas sem a ID opcional fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="2393d-179">Em geral,`id` quando é omitido da URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="2393d-180">`id`é definido como `0` por associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="2393d-181">Nenhuma entidade encontrada na correspondência `id == 0`de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2393d-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="2393d-182">O [Roteamento de atributos](#ar) fornece controle refinado para tornar a ID necessária para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="2393d-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="2393d-183">Por convenção, a documentação inclui parâmetros opcionais `id` como quando é provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="2393d-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="2393d-184">A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas.</span><span class="sxs-lookup"><span data-stu-id="2393d-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="2393d-185">A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="2393d-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="2393d-186">Dá suporte a um esquema de roteamento básico e descritivo.</span><span class="sxs-lookup"><span data-stu-id="2393d-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="2393d-187">É um ponto de partida útil para aplicativos baseados em interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="2393d-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="2393d-188">É o único modelo de rota necessário para muitos aplicativos da interface do usuário da Web.</span><span class="sxs-lookup"><span data-stu-id="2393d-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="2393d-189">Para aplicativos de interface do usuário da Web maiores, outra rota usando [áreas](#areas) , se for sempre tudo o que for necessário.</span><span class="sxs-lookup"><span data-stu-id="2393d-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="2393d-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="2393d-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="2393d-191">Atribua automaticamente um valor de **pedido** aos seus pontos de extremidade com base na ordem em que são invocados.</span><span class="sxs-lookup"><span data-stu-id="2393d-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="2393d-192">Roteamento de ponto de extremidade no ASP.NET Core 3,0 e posterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="2393d-193">Não tem um conceito de rotas.</span><span class="sxs-lookup"><span data-stu-id="2393d-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="2393d-194">Não fornece garantias de ordenação para a execução de extensibilidade, todos os pontos de extremidade são processados de uma só vez.</span><span class="sxs-lookup"><span data-stu-id="2393d-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="2393d-195">Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.</span><span class="sxs-lookup"><span data-stu-id="2393d-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="2393d-196">O [Roteamento de atributos](#ar) é explicado mais adiante neste documento.</span><span class="sxs-lookup"><span data-stu-id="2393d-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="2393d-197">Várias rotas convencionais</span><span class="sxs-lookup"><span data-stu-id="2393d-197">Multiple conventional routes</span></span>

<span data-ttu-id="2393d-198">Várias [rotas convencionais](#cr) podem ser adicionadas `UseEndpoints` no adicionando mais chamadas para <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="2393d-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="2393d-199">Isso permite a definição de várias convenções ou a adição de rotas convencionais que são dedicadas a uma [ação](#action)específica, como:</span><span class="sxs-lookup"><span data-stu-id="2393d-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="2393d-200">A `blog` rota no código anterior é uma **rota convencional dedicada**.</span><span class="sxs-lookup"><span data-stu-id="2393d-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="2393d-201">Ele é chamado de rota convencional dedicada porque:</span><span class="sxs-lookup"><span data-stu-id="2393d-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="2393d-202">Ele usa o [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="2393d-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="2393d-203">Ele é dedicado a uma [ação](#action)específica.</span><span class="sxs-lookup"><span data-stu-id="2393d-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="2393d-204">Porque `controller` e `action` não aparecem no modelo `"blog/{*article}"` de rota como parâmetros:</span><span class="sxs-lookup"><span data-stu-id="2393d-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="2393d-205">Eles só podem ter os valores `{ controller = "Blog", action = "Article" }`padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="2393d-206">Essa rota sempre é mapeada para a `BlogController.Article`ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="2393d-207">`/Blog`, `/Blog/Article`e `/Blog/{any-string}` são os únicos caminhos de URL que correspondem à rota do blog.</span><span class="sxs-lookup"><span data-stu-id="2393d-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="2393d-208">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-208">The preceding example:</span></span>

* <span data-ttu-id="2393d-209">`blog`a rota tem uma prioridade mais alta para correspondências que a `default` rota porque ela é adicionada primeiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="2393d-210">É um exemplo de roteamento de estilo de separador, onde é comum ter um [nome de artigo](https://developer.mozilla.org/docs/Glossary/Slug) como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="2393d-211">No ASP.NET Core 3,0 e posterior, o roteamento não:</span><span class="sxs-lookup"><span data-stu-id="2393d-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="2393d-212">Defina um conceito chamado *rota*.</span><span class="sxs-lookup"><span data-stu-id="2393d-212">Define a concept called a *route*.</span></span> <span data-ttu-id="2393d-213">`UseRouting`Adiciona correspondência de rota ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="2393d-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="2393d-214">O `UseRouting` middleware examina o conjunto de pontos de extremidade definidos no aplicativo e seleciona a melhor correspondência de ponto de extremidades com base na solicitação.</span><span class="sxs-lookup"><span data-stu-id="2393d-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="2393d-215">Forneça garantias sobre a ordem de execução de extensibilidade <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> como <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>ou.</span><span class="sxs-lookup"><span data-stu-id="2393d-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="2393d-216">Consulte [Roteamento](xref:fundamentals/routing) para obter material de referência sobre roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="2393d-217">Ordem de roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="2393d-217">Conventional routing order</span></span>

<span data-ttu-id="2393d-218">O roteamento convencional só corresponde a uma combinação de ação e controlador que são definidos pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="2393d-219">Isso se destina a simplificar os casos em que as rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="2393d-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="2393d-220">Adicionar rotas usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> atribuir automaticamente um valor de pedido a seus pontos de extremidade com base na ordem em que são invocados.</span><span class="sxs-lookup"><span data-stu-id="2393d-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="2393d-221">As correspondências de uma rota que aparece anteriormente têm uma prioridade mais alta.</span><span class="sxs-lookup"><span data-stu-id="2393d-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="2393d-222">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="2393d-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2393d-223">Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="2393d-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="2393d-224">[Rotas convencionais dedicadas](#dcr) com parâmetros de rota Catch `{*article}` -All como podem tornar uma [rota muito grande](xref:fundamentals/routing#greedy), o que significa que ela corresponde às URLs que você pretende corresponder a outras rotas.</span><span class="sxs-lookup"><span data-stu-id="2393d-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="2393d-225">Coloque as rotas de ávido mais tarde na tabela de rotas para evitar correspondências de caminhada.</span><span class="sxs-lookup"><span data-stu-id="2393d-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="2393d-226">Resolvendo ações ambíguas</span><span class="sxs-lookup"><span data-stu-id="2393d-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="2393d-227">Quando dois pontos de extremidade correspondem ao roteamento, o roteamento deve seguir um destes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="2393d-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="2393d-228">Escolha o melhor candidato.</span><span class="sxs-lookup"><span data-stu-id="2393d-228">Choose the best candidate.</span></span>
* <span data-ttu-id="2393d-229">Gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="2393d-229">Throw an exception.</span></span>

<span data-ttu-id="2393d-230">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="2393d-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="2393d-231">O controlador anterior define duas ações que correspondem a:</span><span class="sxs-lookup"><span data-stu-id="2393d-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="2393d-232">O caminho da URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="2393d-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="2393d-233">Rotear `{ controller = Products33, action = Edit, id = 17 }`dados.</span><span class="sxs-lookup"><span data-stu-id="2393d-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="2393d-234">Esse é um padrão típico para controladores MVC:</span><span class="sxs-lookup"><span data-stu-id="2393d-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="2393d-235">`Edit(int)`exibe um formulário para editar um produto.</span><span class="sxs-lookup"><span data-stu-id="2393d-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="2393d-236">`Edit(int, Product)`processa o formulário Postado.</span><span class="sxs-lookup"><span data-stu-id="2393d-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="2393d-237">Para resolver a rota correta:</span><span class="sxs-lookup"><span data-stu-id="2393d-237">To resolve the correct route:</span></span>

* <span data-ttu-id="2393d-238">`Edit(int, Product)`é selecionado quando a solicitação é um HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="2393d-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="2393d-239">`Edit(int)`é selecionado quando o [verbo http](#verb) é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="2393d-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="2393d-240">`Edit(int)`é geralmente chamado por `GET`meio de.</span><span class="sxs-lookup"><span data-stu-id="2393d-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="2393d-241">O <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, é fornecido ao roteamento para que ele possa escolher com base no método http da solicitação.</span><span class="sxs-lookup"><span data-stu-id="2393d-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="2393d-242">O `HttpPostAttribute` faz `Edit(int, Product)` uma correspondência melhor do `Edit(int)`que.</span><span class="sxs-lookup"><span data-stu-id="2393d-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="2393d-243">É importante entender a função de atributos como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="2393d-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="2393d-244">Atributos semelhantes são definidos para outros [verbos HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="2393d-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="2393d-245">No [Roteamento convencional](#cr), é comum que as ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de Mostrar formulário, enviar formulário.</span><span class="sxs-lookup"><span data-stu-id="2393d-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="2393d-246">Por exemplo, consulte [examinar os dois métodos de ação de edição](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="2393d-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="2393d-247">Se o roteamento não puder escolher um melhor candidato <xref:System.Reflection.AmbiguousMatchException> , um será gerado, listando os vários pontos de extremidade correspondentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="2393d-248">Nomes de rota convencionais</span><span class="sxs-lookup"><span data-stu-id="2393d-248">Conventional route names</span></span>

<span data-ttu-id="2393d-249">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rota convencionais:</span><span class="sxs-lookup"><span data-stu-id="2393d-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="2393d-250">Os nomes de rota fornecem um nome lógico à rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-250">The route names give the route a logical name.</span></span> <span data-ttu-id="2393d-251">A rota nomeada pode ser usada para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="2393d-252">O uso de uma rota nomeada simplifica a criação de URL quando a ordenação de rotas pode tornar a geração de URL complicada.</span><span class="sxs-lookup"><span data-stu-id="2393d-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="2393d-253">Os nomes de rota devem ser de todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="2393d-254">Nomes de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-254">Route names:</span></span>

* <span data-ttu-id="2393d-255">Não têm impacto sobre a correspondência de URL ou o tratamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="2393d-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="2393d-256">São usados somente para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-256">Are used only for URL generation.</span></span>

<span data-ttu-id="2393d-257">O conceito de nome de rota é representado no roteamento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="2393d-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="2393d-258">Os termos **nome da rota** e **nome do ponto de extremidade**:</span><span class="sxs-lookup"><span data-stu-id="2393d-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="2393d-259">São intercambiáveis.</span><span class="sxs-lookup"><span data-stu-id="2393d-259">Are interchangeable.</span></span>
* <span data-ttu-id="2393d-260">Qual delas é usada na documentação e no código depende da API que está sendo descrita.</span><span class="sxs-lookup"><span data-stu-id="2393d-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="2393d-261">Roteamento de atributos para APIs REST</span><span class="sxs-lookup"><span data-stu-id="2393d-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="2393d-262">As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por [verbos HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="2393d-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="2393d-263">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="2393d-264">O código `StartUp.Configure` a seguir é típico para uma API REST e é usado no próximo exemplo:</span><span class="sxs-lookup"><span data-stu-id="2393d-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="2393d-265">No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> é chamado dentro `UseEndpoints` para mapear controladores roteados de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="2393d-266">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-266">In the following example:</span></span>

* <span data-ttu-id="2393d-267">O método `Configure` anterior é usado.</span><span class="sxs-lookup"><span data-stu-id="2393d-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="2393d-268">`HomeController`corresponde a um conjunto de URLs semelhantes ao que a rota `{controller=Home}/{action=Index}/{id?}` convencional padrão corresponde.</span><span class="sxs-lookup"><span data-stu-id="2393d-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="2393d-269">A `HomeController.Index` ação é executada para qualquer um dos `/`caminhos de URL `/Home`, `/Home/Index`, ou `/Home/Index/3`.</span><span class="sxs-lookup"><span data-stu-id="2393d-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="2393d-270">Este exemplo realça uma importante diferença de programação entre roteamento de atributo e [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="2393d-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="2393d-271">O roteamento de atributos requer mais entrada para especificar uma rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="2393d-272">A rota padrão convencional lida com as rotas mais sucintamente.</span><span class="sxs-lookup"><span data-stu-id="2393d-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="2393d-273">No entanto, o roteamento de atributos permite e exige um controle preciso de quais modelos de rota se aplicam a cada [ação](#action).</span><span class="sxs-lookup"><span data-stu-id="2393d-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="2393d-274">Com o roteamento de atributos, o nome do controlador e os nomes de ação não desempenham **nenhuma** função na qual a ação é correspondida.</span><span class="sxs-lookup"><span data-stu-id="2393d-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="2393d-275">O exemplo a seguir corresponde às mesmas URLs do exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="2393d-276">O código a seguir usa substituição de `action` token `controller`para e:</span><span class="sxs-lookup"><span data-stu-id="2393d-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="2393d-277">O código a seguir `[Route("[controller]/[action]")]` se aplica ao controlador:</span><span class="sxs-lookup"><span data-stu-id="2393d-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="2393d-278">No código anterior, os modelos `Index` de método devem preceder `/` ou `~/` até os modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="2393d-279">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="2393d-280">Consulte [precedência do modelo de rota](xref:fundamentals/routing#rtp) para obter informações sobre a seleção do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="2393d-281">Nomes reservados de roteamento</span><span class="sxs-lookup"><span data-stu-id="2393d-281">Reserved routing names</span></span>

<span data-ttu-id="2393d-282">As seguintes palavras-chave são nomes de parâmetro de rota reservados ao usar controladores ou Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="2393d-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="2393d-283">O `page` uso de como um parâmetro de rota com roteamento de atributo é um erro comum.</span><span class="sxs-lookup"><span data-stu-id="2393d-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="2393d-284">Fazer isso resulta em comportamento inconsistente e confuso com a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="2393d-285">Os nomes de parâmetro especiais são usados pela geração de URL para determinar se uma operação de geração de URL refere-se a uma página Razor ou a um controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="2393d-286">Modelos de verbo HTTP</span><span class="sxs-lookup"><span data-stu-id="2393d-286">HTTP verb templates</span></span>

<span data-ttu-id="2393d-287">ASP.NET Core tem os seguintes modelos de verbo HTTP:</span><span class="sxs-lookup"><span data-stu-id="2393d-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="2393d-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="2393d-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="2393d-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="2393d-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="2393d-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="2393d-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="2393d-294">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="2393d-294">Route templates</span></span>

<span data-ttu-id="2393d-295">ASP.NET Core tem os seguintes modelos de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="2393d-296">Todos os [modelos de verbo http](#verb) são modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="2393d-297">[Rota](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="2393d-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="2393d-298">Roteamento de atributos com atributos de verbo http</span><span class="sxs-lookup"><span data-stu-id="2393d-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="2393d-299">Considere o seguinte controlador:</span><span class="sxs-lookup"><span data-stu-id="2393d-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="2393d-300">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-300">In the preceding code:</span></span>

* <span data-ttu-id="2393d-301">Cada ação contém o `[HttpGet]` atributo, que restringe a correspondência somente a solicitações HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="2393d-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="2393d-302">A `GetProduct` ação inclui o `"{id}"` modelo, portanto `id` , é anexada ao `"api/[controller]"` modelo no controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="2393d-303">O modelo de métodos `"api/[controller]/"{id}""`é.</span><span class="sxs-lookup"><span data-stu-id="2393d-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="2393d-304">Portanto, essa ação só corresponde a solicitações GET de para `/api/test2/xyz`o`/api/test2/123`formulário`/api/test2/{any string}`,,, etc.</span><span class="sxs-lookup"><span data-stu-id="2393d-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="2393d-305">A `GetIntProduct` ação contém o `"int/{id:int}")` modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="2393d-306">A `:int` parte do modelo restringe os `id` valores de rota para cadeias de caracteres que podem ser convertidas em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="2393d-307">Uma solicitação GET para `/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="2393d-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="2393d-308">Não corresponde a esta ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="2393d-309">Retorna um erro [404 não encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="2393d-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="2393d-310">A `GetInt2Product` ação contém `{id}` no modelo, mas não restringe `id` a valores que podem ser convertidos em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="2393d-311">Uma solicitação GET para `/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="2393d-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="2393d-312">Corresponde a essa rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-312">Matches this route.</span></span>
  * <span data-ttu-id="2393d-313">A associação de modelo falha `abc` ao converter em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="2393d-314">O `id` parâmetro do método é inteiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="2393d-315">Retorna uma [solicitação inadequada 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque a associação de modelo`abc` não pôde ser convertida em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="2393d-316">O <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>roteamento de atributos pode usar atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="2393d-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="2393d-317">Todos os atributos de [verbo http](#verb) aceitam um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="2393d-318">O exemplo a seguir mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="2393d-319">Usando o caminho `/products3`da URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="2393d-320">A `MyProductsController.ListProducts` ação é executada quando o [verbo http](#verb) é `GET`.</span><span class="sxs-lookup"><span data-stu-id="2393d-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="2393d-321">A `MyProductsController.CreateProduct` ação é executada quando o [verbo http](#verb) é `POST`.</span><span class="sxs-lookup"><span data-stu-id="2393d-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="2393d-322">Ao criar uma API REST, é raro que você precise usar `[Route(...)]` em um método de ação, pois a ação aceita todos os métodos http.</span><span class="sxs-lookup"><span data-stu-id="2393d-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="2393d-323">É melhor usar o [atributo de verbo http](#verb) mais específico para ser preciso em relação ao que sua API dá suporte.</span><span class="sxs-lookup"><span data-stu-id="2393d-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="2393d-324">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="2393d-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="2393d-325">As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="2393d-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="2393d-326">Isso significa que muitas operações, por exemplo, GET e POST no mesmo recurso lógico usam a mesma URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="2393d-327">O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.</span><span class="sxs-lookup"><span data-stu-id="2393d-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="2393d-328">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="2393d-329">No exemplo a seguir, `id` é necessário como parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="2393d-330">A `Products2ApiController.GetProduct(int)` ação:</span><span class="sxs-lookup"><span data-stu-id="2393d-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="2393d-331">É executado com caminho de URL como`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="2393d-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="2393d-332">Não é executado com o caminho `/products2`da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="2393d-333">O atributo [[consume]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que uma ação limite os tipos de conteúdo de solicitação com suporte.</span><span class="sxs-lookup"><span data-stu-id="2393d-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="2393d-334">Para obter mais informações, consulte [definir tipos de conteúdo de solicitação com suporte com o atributo consumes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="2393d-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="2393d-335">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="2393d-336">Para obter mais informações `[ApiController]`sobre o, consulte o [atributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="2393d-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="2393d-337">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="2393d-337">Route name</span></span>

<span data-ttu-id="2393d-338">O código a seguir define um nome da rota como `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="2393d-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="2393d-339">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="2393d-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="2393d-340">Nomes de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-340">Route names:</span></span>

* <span data-ttu-id="2393d-341">Não têm impacto sobre o comportamento de correspondência de URL do roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="2393d-342">São usados somente para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-342">Are only used for URL generation.</span></span>

<span data-ttu-id="2393d-343">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="2393d-344">Compare o código anterior com a rota padrão convencional, que define o `id` parâmetro como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="2393d-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="2393d-345">A capacidade de especificar precisamente as APIs tem vantagens, como permitir `/products` e `/products/5` ser expedida para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="2393d-346">Combinando rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-346">Combining attribute routes</span></span>

<span data-ttu-id="2393d-347">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="2393d-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="2393d-348">Modelos de rota definidos no controlador precedem modelos de rota nas ações. </span><span class="sxs-lookup"><span data-stu-id="2393d-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="2393d-349">Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="2393d-350">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-350">In the preceding example:</span></span>

* <span data-ttu-id="2393d-351">O caminho `/products` da URL pode corresponder`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="2393d-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="2393d-352">O caminho `/products/5` da URL pode `ProductsApi.GetProduct(int)`corresponder.</span><span class="sxs-lookup"><span data-stu-id="2393d-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="2393d-353">Ambas as ações correspondem apenas a HTTP `GET` porque elas são marcadas com `[HttpGet]` o atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="2393d-354">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="2393d-355">O exemplo a seguir corresponde a um conjunto de caminhos de URL semelhante à rota padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="2393d-356">A tabela a seguir explica `[Route]` os atributos no código anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="2393d-357">Atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-357">Attribute</span></span>               | <span data-ttu-id="2393d-358">Combina com`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="2393d-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="2393d-359">Define o modelo de rota</span><span class="sxs-lookup"><span data-stu-id="2393d-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="2393d-360">Sim</span><span class="sxs-lookup"><span data-stu-id="2393d-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="2393d-361">Sim</span><span class="sxs-lookup"><span data-stu-id="2393d-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="2393d-362">**Não**</span><span class="sxs-lookup"><span data-stu-id="2393d-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="2393d-363">Sim</span><span class="sxs-lookup"><span data-stu-id="2393d-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="2393d-364">Ordem de rota de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-364">Attribute route order</span></span>

<span data-ttu-id="2393d-365">O roteamento cria uma árvore e corresponde a todos os pontos de extremidade simultaneamente:</span><span class="sxs-lookup"><span data-stu-id="2393d-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="2393d-366">As entradas de rota se comportam como se colocadas em uma ordenação ideal.</span><span class="sxs-lookup"><span data-stu-id="2393d-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="2393d-367">As rotas mais específicas têm a chance de serem executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="2393d-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="2393d-368">Por exemplo, uma rota de atributo `blog/search/{topic}` como é mais específica do que uma rota `blog/{*article}`de atributo como.</span><span class="sxs-lookup"><span data-stu-id="2393d-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="2393d-369">A `blog/search/{topic}` rota tem prioridade mais alta, por padrão, porque é mais específica.</span><span class="sxs-lookup"><span data-stu-id="2393d-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="2393d-370">Usando o [Roteamento convencional](#cr), o desenvolvedor é responsável por colocar rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="2393d-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="2393d-371">As rotas de atributo podem configurar um pedido <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> usando a propriedade.</span><span class="sxs-lookup"><span data-stu-id="2393d-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="2393d-372">Todos os [atributos de rota](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fornecidos pelo Framework `Order` incluem.</span><span class="sxs-lookup"><span data-stu-id="2393d-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="2393d-373">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="2393d-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="2393d-374">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="2393d-374">The default order is `0`.</span></span> <span data-ttu-id="2393d-375">Definir uma rota usando `Order = -1` execuções antes das rotas que não definem um pedido.</span><span class="sxs-lookup"><span data-stu-id="2393d-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="2393d-376">Definir uma rota usando `Order = 1` execuções após a ordenação de rota padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="2393d-377">**Evite** , dependendo de `Order`.</span><span class="sxs-lookup"><span data-stu-id="2393d-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="2393d-378">Se o espaço de URL de um aplicativo exigir valores de ordem explícitos para rotear corretamente, isso provavelmente será confuso para os clientes também.</span><span class="sxs-lookup"><span data-stu-id="2393d-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="2393d-379">Em geral, o roteamento de atributos seleciona a rota correta com correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="2393d-380">Se a ordem padrão usada para a geração de URL não estiver funcionando, o uso de um nome de rota como uma substituição normalmente `Order` será mais simples do que aplicar a propriedade.</span><span class="sxs-lookup"><span data-stu-id="2393d-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="2393d-381">Considere os dois controladores a seguir que definem a rota `/home`correspondente:</span><span class="sxs-lookup"><span data-stu-id="2393d-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="2393d-382">A `/home` solicitação com o código anterior gera uma exceção semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="2393d-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="2393d-383">Adicionar `Order` a um dos atributos de rota resolve a ambiguidade:</span><span class="sxs-lookup"><span data-stu-id="2393d-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="2393d-384">Com o código anterior, `/home` executa o `HomeController.Index` ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="2393d-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="2393d-385">Para obter a `MyDemoController.MyIndex`solicitação `/home/MyIndex`,.</span><span class="sxs-lookup"><span data-stu-id="2393d-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="2393d-386">**Observação**:</span><span class="sxs-lookup"><span data-stu-id="2393d-386">**Note**:</span></span>

* <span data-ttu-id="2393d-387">O código anterior é um exemplo de design de roteamento insatisfatório.</span><span class="sxs-lookup"><span data-stu-id="2393d-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="2393d-388">Ele foi usado para ilustrar `Order` a propriedade.</span><span class="sxs-lookup"><span data-stu-id="2393d-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="2393d-389">A `Order` Propriedade resolve apenas a ambiguidade, esse modelo não pode ser correspondido.</span><span class="sxs-lookup"><span data-stu-id="2393d-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="2393d-390">Seria melhor remover o `[Route("Home")]` modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="2393d-391">Consulte [ Razor as páginas rota e convenções de aplicativo: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order) para obter informações Razor sobre a ordem de rota com páginas.</span><span class="sxs-lookup"><span data-stu-id="2393d-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="2393d-392">Em alguns casos, um erro HTTP 500 é retornado com rotas ambíguas.</span><span class="sxs-lookup"><span data-stu-id="2393d-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="2393d-393">Use o [registro em log](xref:fundamentals/logging/index) para ver quais pontos de `AmbiguousMatchException`extremidade causaram o.</span><span class="sxs-lookup"><span data-stu-id="2393d-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="2393d-394">Substituição de token em modelos de rota [controlador], [ação], [área]</span><span class="sxs-lookup"><span data-stu-id="2393d-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="2393d-395">Para sua conveniência, as rotas de atributo dão suporte à substituição de token para parâmetros de rota reservados ao colocar um token em um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="2393d-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="2393d-396">Colchetes:`[]`</span><span class="sxs-lookup"><span data-stu-id="2393d-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="2393d-397">Chaves:`{}`</span><span class="sxs-lookup"><span data-stu-id="2393d-397">Curly braces: `{}`</span></span>

<span data-ttu-id="2393d-398">Os `[action]`tokens `[area]`,, `[controller]` e são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida:</span><span class="sxs-lookup"><span data-stu-id="2393d-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="2393d-399">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="2393d-400">Acordo`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="2393d-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="2393d-401">Acordo`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="2393d-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="2393d-402">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="2393d-403">O exemplo anterior se comporta da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="2393d-404">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="2393d-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="2393d-405">Isso é poderoso combinado com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="2393d-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="2393d-406">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="2393d-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`gera um nome de rota exclusivo para cada ação:</span><span class="sxs-lookup"><span data-stu-id="2393d-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="2393d-408">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="2393d-409"> gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="2393d-410">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="2393d-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="2393d-411">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="2393d-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="2393d-412">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="2393d-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="2393d-413">Um transformador de parâmetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="2393d-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="2393d-414">Por exemplo, um transformador de parâmetro personalizado `SlugifyParameterTransformer` altera `SubscriptionManagement` o valor de `subscription-management`rota para:</span><span class="sxs-lookup"><span data-stu-id="2393d-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="2393d-415">O <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="2393d-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="2393d-416">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="2393d-417">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="2393d-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="2393d-418">O método `ListAll` anterior corresponde `/subscription-management/list-all`a.</span><span class="sxs-lookup"><span data-stu-id="2393d-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="2393d-419">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2393d-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="2393d-420">Consulte [MDN Web docs no](https://developer.mozilla.org/docs/Glossary/Slug) separador para obter a definição do separador.</span><span class="sxs-lookup"><span data-stu-id="2393d-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="2393d-421">Várias rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="2393d-421">Multiple attribute routes</span></span>

<span data-ttu-id="2393d-422">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="2393d-423">O uso mais comum desse recurso é para simular o comportamento da rota convencional padrão, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="2393d-424">Colocar vários atributos de rota no controlador significa que cada um combina com cada um dos atributos de rota nos métodos de ação:</span><span class="sxs-lookup"><span data-stu-id="2393d-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="2393d-425">Todas as restrições de rota de [verbo http](#verb) implementam `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="2393d-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="2393d-426">Quando vários atributos de rota implementados <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> são colocados em uma ação:</span><span class="sxs-lookup"><span data-stu-id="2393d-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="2393d-427">Cada restrição de ação combina com o modelo de rota aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="2393d-428">O uso de várias rotas em ações pode parecer útil e eficiente, é melhor manter o espaço de URL básico e bem definido do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="2393d-429">Use várias rotas em ações **somente** quando necessário, por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="2393d-430">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="2393d-431">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="2393d-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="2393d-432">No código anterior, `[HttpPost("product/{id:int}")]` o aplica uma restrição de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="2393d-433">A `ProductsController.ShowProduct` ação é correspondida somente por caminhos de URL `/product/3`como.</span><span class="sxs-lookup"><span data-stu-id="2393d-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="2393d-434">A parte `{id:int}` do modelo de rota restringe esse segmento a apenas inteiros.</span><span class="sxs-lookup"><span data-stu-id="2393d-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="2393d-435">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="2393d-436">Atributos de rota personalizados usando IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="2393d-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="2393d-437">Todos os [atributos de rota](#rt) implementam <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="2393d-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="2393d-438">O tempo de execução de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2393d-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="2393d-439">Procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="2393d-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="2393d-440">Usa os atributos que implementam `IRouteTemplateProvider` para compilar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="2393d-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="2393d-441">Implemente `IRouteTemplateProvider` para definir atributos de rota personalizados.</span><span class="sxs-lookup"><span data-stu-id="2393d-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="2393d-442">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="2393d-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="2393d-443">O método `Get` anterior retorna `Order = 2, Template = api/MyTestApi`.</span><span class="sxs-lookup"><span data-stu-id="2393d-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="2393d-444">Usar o modelo de aplicativo para personalizar as rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="2393d-445">O modelo de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2393d-445">The application model:</span></span>

* <span data-ttu-id="2393d-446">É um modelo de objeto criado na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2393d-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="2393d-447">Contém todos os metadados usados pelo ASP.NET Core para rotear e executar as ações em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="2393d-448">O modelo de aplicativo inclui todos os dados coletados dos atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="2393d-449">Os dados dos atributos de rota são fornecidos pela `IRouteTemplateProvider` implementação.</span><span class="sxs-lookup"><span data-stu-id="2393d-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="2393d-450">Regras</span><span class="sxs-lookup"><span data-stu-id="2393d-450">Conventions:</span></span>

* <span data-ttu-id="2393d-451">Pode ser escrito para modificar o modelo de aplicativo para personalizar a forma como o roteamento se comporta.</span><span class="sxs-lookup"><span data-stu-id="2393d-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="2393d-452">São lidos na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-452">Are read at app startup.</span></span>

<span data-ttu-id="2393d-453">Esta seção mostra um exemplo básico de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="2393d-454">O código a seguir torna as rotas aproximadamente alinhadas com a estrutura de pastas do projeto.</span><span class="sxs-lookup"><span data-stu-id="2393d-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="2393d-455">O código a seguir impede `namespace` que a Convenção seja aplicada a controladores que são roteados por atributo:</span><span class="sxs-lookup"><span data-stu-id="2393d-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="2393d-456">Por exemplo, o controlador a seguir não `NamespaceRoutingConvention`usa:</span><span class="sxs-lookup"><span data-stu-id="2393d-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2393d-457">O método `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="2393d-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="2393d-458">Não fará nada se o controlador for um atributo roteado.</span><span class="sxs-lookup"><span data-stu-id="2393d-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="2393d-459">Define o modelo de controladores com base `namespace`no, com a `namespace` base removida.</span><span class="sxs-lookup"><span data-stu-id="2393d-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="2393d-460">O `NamespaceRoutingConvention` pode ser aplicado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2393d-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="2393d-461">Por exemplo, considere o seguinte controlador:</span><span class="sxs-lookup"><span data-stu-id="2393d-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="2393d-462">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="2393d-462">In the preceding code:</span></span>

* <span data-ttu-id="2393d-463">A base `namespace` é `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="2393d-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="2393d-464">O nome completo do controlador anterior é `My.Application.Admin.Controllers.UsersController`.</span><span class="sxs-lookup"><span data-stu-id="2393d-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="2393d-465">O `NamespaceRoutingConvention` define o modelo de controladores `Admin/Controllers/Users/[action]/{id?`como.</span><span class="sxs-lookup"><span data-stu-id="2393d-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="2393d-466">O `NamespaceRoutingConvention` também pode ser aplicado como um atributo em um controlador:</span><span class="sxs-lookup"><span data-stu-id="2393d-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="2393d-467">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="2393d-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="2393d-468">ASP.NET Core aplicativos podem misturar o uso de roteamento convencional e roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="2393d-469">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="2393d-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="2393d-470">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2393d-471">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2393d-472">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="2393d-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="2393d-473">**Qualquer** atributo de rota no controlador faz com que **todas as** ações no atributo do controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="2393d-474">Roteamento de atributos e roteamento convencional usam o mesmo mecanismo de roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="2393d-475">Geração de URL e valores de ambiente</span><span class="sxs-lookup"><span data-stu-id="2393d-475">URL Generation and ambient values</span></span>

<span data-ttu-id="2393d-476">Os aplicativos podem usar os recursos de geração de URL de roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="2393d-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="2393d-477">A geração de URLs elimina URLs de codificação, tornando o código mais robusto e passível de manutenção.</span><span class="sxs-lookup"><span data-stu-id="2393d-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="2393d-478">Esta seção se concentra nos recursos de geração de URL fornecidos pelo MVC e aborda apenas noções básicas de como funciona a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="2393d-479">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="2393d-480">A <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface é o elemento subjacente da infraestrutura entre o MVC e o roteamento para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="2393d-481">Uma instância do `IUrlHelper` está disponível por meio `Url` da propriedade em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="2393d-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="2393d-482">No exemplo a seguir, a `IUrlHelper` interface é usada por meio `Controller.Url` da propriedade para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="2393d-483">Se o aplicativo estiver usando a rota convencional padrão, o valor da `url` variável será a cadeia de caracteres `/UrlGeneration/Destination`do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="2393d-484">Esse caminho de URL é criado pelo roteamento combinando:</span><span class="sxs-lookup"><span data-stu-id="2393d-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="2393d-485">Os valores de rota da solicitação atual, que são chamados de **valores de ambiente**.</span><span class="sxs-lookup"><span data-stu-id="2393d-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="2393d-486">Os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="2393d-487">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2393d-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="2393d-488">Um parâmetro de rota que não tem um valor pode:</span><span class="sxs-lookup"><span data-stu-id="2393d-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="2393d-489">Use um valor padrão se ele tiver um.</span><span class="sxs-lookup"><span data-stu-id="2393d-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="2393d-490">Será ignorado se for opcional.</span><span class="sxs-lookup"><span data-stu-id="2393d-490">Be skipped if it's optional.</span></span> <span data-ttu-id="2393d-491">Por exemplo, o `id` do modelo `{controller}/{action}/{id?}`de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="2393d-492">A geração de URL falhará se qualquer parâmetro de rota necessário não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="2393d-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="2393d-493">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="2393d-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="2393d-494">O exemplo anterior de `Url.Action` assume o [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="2393d-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="2393d-495">A geração de URL funciona de forma semelhante ao [Roteamento de atributos](#ar), embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="2393d-496">Com roteamento convencional:</span><span class="sxs-lookup"><span data-stu-id="2393d-496">With conventional routing:</span></span>

* <span data-ttu-id="2393d-497">Os valores de rota são usados para expandir um modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="2393d-498">Os valores de rota `controller` para `action` e geralmente aparecem nesse modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="2393d-499">Isso funciona porque as URLs correspondidas pelo roteamento aderem a uma convenção.</span><span class="sxs-lookup"><span data-stu-id="2393d-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="2393d-500">O exemplo a seguir usa roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="2393d-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="2393d-501">A `Source` ação no código anterior gera `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="2393d-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="2393d-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>foi adicionado no ASP.NET Core 3,0 como uma alternativa ao `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="2393d-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="2393d-503">`LinkGenerator`oferece funcionalidade semelhante, mas mais flexível.</span><span class="sxs-lookup"><span data-stu-id="2393d-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="2393d-504">Cada método em `IUrlHelper` também tem uma família correspondente de métodos `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="2393d-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="2393d-505">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="2393d-505">Generating URLs by action name</span></span>

<span data-ttu-id="2393d-506">A [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e todas as sobrecargas relacionadas são projetadas para gerar o ponto de extremidade de destino especificando um nome de controlador e um nome de ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="2393d-507">Ao usar `Url.Action`, os valores de rota atuais `controller` para `action` e são fornecidos pelo tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="2393d-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="2393d-508">O valor de `controller` e `action` são parte de [valores de ambiente](#ambient) e valores.</span><span class="sxs-lookup"><span data-stu-id="2393d-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="2393d-509">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.</span><span class="sxs-lookup"><span data-stu-id="2393d-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="2393d-510">O roteamento tenta usar os valores em valores de ambiente para preencher as informações que não foram fornecidas durante a geração de uma URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="2393d-511">Considere uma rota como `{a}/{b}/{c}/{d}` com valores `{ a = Alice, b = Bob, c = Carol, d = David }`de ambiente:</span><span class="sxs-lookup"><span data-stu-id="2393d-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="2393d-512">O roteamento tem informações suficientes para gerar uma URL sem valores adicionais.</span><span class="sxs-lookup"><span data-stu-id="2393d-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="2393d-513">O roteamento tem informações suficientes porque todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="2393d-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="2393d-514">Se o valor `{ d = Donovan }` for adicionado:</span><span class="sxs-lookup"><span data-stu-id="2393d-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="2393d-515">O valor `{ d = David }` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="2393d-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="2393d-516">O caminho de URL gerado `Alice/Bob/Carol/Donovan`é.</span><span class="sxs-lookup"><span data-stu-id="2393d-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="2393d-517">**Aviso**: os caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="2393d-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="2393d-518">No exemplo anterior, se o valor `{ c = Cheryl }` for adicionado:</span><span class="sxs-lookup"><span data-stu-id="2393d-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="2393d-519">Ambos os valores `{ c = Carol, d = David }` são ignorados.</span><span class="sxs-lookup"><span data-stu-id="2393d-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="2393d-520">Não há mais um valor para e `d` a geração de URL falha.</span><span class="sxs-lookup"><span data-stu-id="2393d-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="2393d-521">Os valores `c` desejados `d` de e devem ser especificados para gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="2393d-522">Talvez você espere atingir esse problema com a rota `{controller}/{action}/{id?}`padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="2393d-523">Esse problema é raro na prática porque `Url.Action` o sempre especifica explicitamente `controller` um `action` valor e.</span><span class="sxs-lookup"><span data-stu-id="2393d-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="2393d-524">Várias sobrecargas de [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) usam um objeto de valores de rota para fornecer valores para parâmetros de `controller` rota `action`diferentes de e.</span><span class="sxs-lookup"><span data-stu-id="2393d-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="2393d-525">O objeto de valores de rota é usado `id`com frequência com.</span><span class="sxs-lookup"><span data-stu-id="2393d-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="2393d-526">Por exemplo, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="2393d-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="2393d-527">O objeto de valores de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-527">The route values object:</span></span>

* <span data-ttu-id="2393d-528">Por convenção geralmente é um objeto de tipo anônimo.</span><span class="sxs-lookup"><span data-stu-id="2393d-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="2393d-529">Pode ser um `IDictionary<>` ou um [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="2393d-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="2393d-530">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="2393d-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="2393d-531">O código anterior gera `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="2393d-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="2393d-532">O código a seguir gera uma URL absoluta:</span><span class="sxs-lookup"><span data-stu-id="2393d-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="2393d-533">Para criar uma URL absoluta, use um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="2393d-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="2393d-534">Uma sobrecarga que aceita um `protocol`.</span><span class="sxs-lookup"><span data-stu-id="2393d-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="2393d-535">Por exemplo, o código anterior.</span><span class="sxs-lookup"><span data-stu-id="2393d-535">For example, the preceding code.</span></span>
* <span data-ttu-id="2393d-536">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que gera URIs absolutos por padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="2393d-537">Gerar URLs por rota</span><span class="sxs-lookup"><span data-stu-id="2393d-537">Generate URLs by route</span></span>

<span data-ttu-id="2393d-538">O código anterior demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="2393d-539">`IUrlHelper`também fornece a família de métodos [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="2393d-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="2393d-540">Esses métodos são semelhantes a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="2393d-541">O uso mais comum de `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="2393d-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="2393d-542">Especifica um nome de rota para gerar a URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="2393d-543">Geralmente, não especifica um controlador ou um nome de ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="2393d-544">O arquivo Razor a seguir gera um link HTML para `Destination_Route`:</span><span class="sxs-lookup"><span data-stu-id="2393d-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="2393d-545">Gerar URLs em HTML eRazor</span><span class="sxs-lookup"><span data-stu-id="2393d-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="2393d-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>fornece os <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> métodos [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para gerar `<form>` e `<a>` elementos, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="2393d-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="2393d-547">Esses métodos usam o método [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="2393d-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="2393d-548">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="2393d-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="2393d-549">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="2393d-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="2393d-550">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="2393d-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="2393d-551">Consulte [auxiliares de marca em formulários](xref:mvc/views/working-with-forms) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="2393d-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="2393d-552">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="2393d-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="2393d-553">Geração de URL nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="2393d-553">URL generation in Action Results</span></span>

<span data-ttu-id="2393d-554">Os exemplos anteriores mostraram `IUrlHelper` o uso de em um controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="2393d-555">O uso mais comum em um controlador é gerar uma URL como parte de um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="2393d-556">As classes base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="2393d-557">Um uso típico é redirecionar depois de aceitar a entrada do usuário:</span><span class="sxs-lookup"><span data-stu-id="2393d-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="2393d-558">A ação resulta em métodos de fábrica <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> e seguem um padrão semelhante para os métodos `IUrlHelper`em.</span><span class="sxs-lookup"><span data-stu-id="2393d-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="2393d-559">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="2393d-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="2393d-560">O [Roteamento convencional](#cr) pode usar um tipo especial de definição de rota chamada de [rota convencional dedicada](#dcr).</span><span class="sxs-lookup"><span data-stu-id="2393d-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="2393d-561">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada:</span><span class="sxs-lookup"><span data-stu-id="2393d-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="2393d-562">Usando as definições de rota anteriores `Url.Action("Index", "Home")` , o gera o `/` caminho da `default` URL usando a rota, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="2393d-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="2393d-563">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="2393d-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="2393d-564">As [rotas convencionais dedicadas](#dcr) contam com um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impede que a rota seja muito [ávido](xref:fundamentals/routing#greedy) com a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="2393d-565">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="2393d-566">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="2393d-567">A geração de `blog` URL usando falha porque `{ controller = Home, action = Index }` os valores `{ controller = Blog, action = Article }`não correspondem.</span><span class="sxs-lookup"><span data-stu-id="2393d-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="2393d-568">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="2393d-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="2393d-569">Áreas</span><span class="sxs-lookup"><span data-stu-id="2393d-569">Areas</span></span>

<span data-ttu-id="2393d-570">As [áreas](xref:mvc/controllers/areas) são um recurso do MVC usado para organizar a funcionalidade relacionada em um grupo como separado:</span><span class="sxs-lookup"><span data-stu-id="2393d-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="2393d-571">Namespace de roteamento para ações do controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="2393d-572">Estrutura de pastas para exibições.</span><span class="sxs-lookup"><span data-stu-id="2393d-572">Folder structure for views.</span></span>

<span data-ttu-id="2393d-573">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que eles tenham áreas diferentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="2393d-574">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="2393d-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="2393d-575">Esta seção discute como o roteamento interage com áreas.</span><span class="sxs-lookup"><span data-stu-id="2393d-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="2393d-576">Consulte [áreas](xref:mvc/controllers/areas) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="2393d-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="2393d-577">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma `area` rota para um `area` nome `Blog`:</span><span class="sxs-lookup"><span data-stu-id="2393d-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="2393d-578">No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> é chamado para criar o. `"blog_route"`</span><span class="sxs-lookup"><span data-stu-id="2393d-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="2393d-579">O segundo parâmetro, `"Blog"`, é o nome da área.</span><span class="sxs-lookup"><span data-stu-id="2393d-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="2393d-580">Ao fazer a correspondência de um `/Manage/Users/AddUser`caminho de `"blog_route"` URL como, a rota `{ area = Blog, controller = Users, action = AddUser }`gera os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="2393d-581">O `area` valor de rota é produzido por um valor padrão `area`para.</span><span class="sxs-lookup"><span data-stu-id="2393d-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="2393d-582">A rota criada pelo `MapAreaControllerRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="2393d-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="2393d-583">`MapAreaControllerRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2393d-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="2393d-584">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="2393d-585">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="2393d-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2393d-586">Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="2393d-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2393d-587">Usando o exemplo anterior, os valores `{ area = Blog, controller = Users, action = AddUser }` de rota correspondem à seguinte ação:</span><span class="sxs-lookup"><span data-stu-id="2393d-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="2393d-588">O atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) é o que denota um controlador como parte de uma área.</span><span class="sxs-lookup"><span data-stu-id="2393d-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="2393d-589">Esse controlador está na `Blog` área.</span><span class="sxs-lookup"><span data-stu-id="2393d-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="2393d-590">Os controladores sem `[Area]` um atributo não são membros de nenhuma área e **não** correspondem quando o valor `area` de rota é fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="2393d-591">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="2393d-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="2393d-592">O namespace de cada controlador é mostrado aqui para fins de integridade.</span><span class="sxs-lookup"><span data-stu-id="2393d-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="2393d-593">Se os controladores anteriores usarem o mesmo namespace, um erro do compilador será gerado.</span><span class="sxs-lookup"><span data-stu-id="2393d-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="2393d-594">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="2393d-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="2393d-595">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="2393d-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="2393d-596">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="2393d-597">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="2393d-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="2393d-598">Ao executar uma ação dentro de uma área, o valor de `area` rota para está disponível como um [valor de ambiente](#ambient) para roteamento a ser usado para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="2393d-599">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="2393d-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="2393d-600">O código a seguir gera uma URL `/Zebra/Users/AddUser`para:</span><span class="sxs-lookup"><span data-stu-id="2393d-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="2393d-601">Definição de ação</span><span class="sxs-lookup"><span data-stu-id="2393d-601">Action definition</span></span>

<span data-ttu-id="2393d-602">Os métodos públicos em um controlador, exceto aqueles com o atributo [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , são ações.</span><span class="sxs-lookup"><span data-stu-id="2393d-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="2393d-603">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="2393d-603">Sample code</span></span>

 * <span data-ttu-id="2393d-604">O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="2393d-605">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2393d-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2393d-606">O ASP.NET Core MVC usa o [middleware](xref:fundamentals/middleware/index) de Roteamento para fazer as correspondências das URLs de solicitações de entrada e mapeá-las para ações.</span><span class="sxs-lookup"><span data-stu-id="2393d-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="2393d-607">As rotas são definidas em atributos ou no código de inicialização.</span><span class="sxs-lookup"><span data-stu-id="2393d-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="2393d-608">Elas descrevem como deve ser feita a correspondência entre caminhos de URL e ações.</span><span class="sxs-lookup"><span data-stu-id="2393d-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="2393d-609">As rotas também são usadas para gerar URLs (para links) enviados em resposta.</span><span class="sxs-lookup"><span data-stu-id="2393d-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="2393d-610">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2393d-611">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2393d-612">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="2393d-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="2393d-613">Este documento explicará as interações entre o MVC e o roteamento e como aplicativos MVC comuns usam recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="2393d-614">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes sobre o roteamento avançado.</span><span class="sxs-lookup"><span data-stu-id="2393d-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="2393d-615">Configurando o middleware de Roteamento</span><span class="sxs-lookup"><span data-stu-id="2393d-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="2393d-616">No método *Configurar*, você poderá ver código semelhante a:</span><span class="sxs-lookup"><span data-stu-id="2393d-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2393d-617">Dentro da chamada para `UseMvc`, `MapRoute` é usado para criar uma única rota, que chamaremos de rota `default`.</span><span class="sxs-lookup"><span data-stu-id="2393d-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="2393d-618">A maioria dos aplicativos MVC usa uma rota com um modelo semelhante à rota `default`.</span><span class="sxs-lookup"><span data-stu-id="2393d-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="2393d-619">O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` pode corresponder a um caminho de URL como `/Products/Details/5` e extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` gerando tokens para o caminho.</span><span class="sxs-lookup"><span data-stu-id="2393d-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="2393d-620">O MVC tentará localizar um controlador chamado `ProductsController` e executar a ação `Details`:</span><span class="sxs-lookup"><span data-stu-id="2393d-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="2393d-621">Observe que, neste exemplo, o model binding usaria o valor de `id = 5` para definir o parâmetro `id` como `5` ao invocar essa ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="2393d-622">Consulte [Model binding](../models/model-binding.md) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="2393d-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="2393d-623">Usando a rota `default`:</span><span class="sxs-lookup"><span data-stu-id="2393d-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2393d-624">O modelo da rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-624">The route template:</span></span>

* <span data-ttu-id="2393d-625">`{controller=Home}` define `Home` como o `controller` padrão</span><span class="sxs-lookup"><span data-stu-id="2393d-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="2393d-626">`{action=Index}` define `Index` como o `action` padrão</span><span class="sxs-lookup"><span data-stu-id="2393d-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="2393d-627">`{id?}` define `id` como opcional</span><span class="sxs-lookup"><span data-stu-id="2393d-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="2393d-628">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="2393d-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="2393d-629">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="2393d-630">`"{controller=Home}/{action=Index}/{id?}"` pode corresponder ao caminho da URL `/` e produzirá os valores de rota `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="2393d-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="2393d-631">Os valores de `controller` e `action` usam os valores padrão, `id` não produz um valor, uma vez que não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="2393d-632">O MVC usaria esses valores de rota para selecionar a ação `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="2393d-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="2393d-633">Usando essa definição de controlador e modelo de rota, a ação `HomeController.Index` seria executada para qualquer um dos caminhos de URL a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="2393d-634">O método de conveniência `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="2393d-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="2393d-635">Pode ser usado para substituir:</span><span class="sxs-lookup"><span data-stu-id="2393d-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2393d-636">`UseMvc` e `UseMvcWithDefaultRoute` adicionam uma instância de `RouterMiddleware` ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="2393d-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="2393d-637">O MVC não interage diretamente com o middleware e usa o roteamento para tratar das solicitações.</span><span class="sxs-lookup"><span data-stu-id="2393d-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="2393d-638">O MVC é conectado às rotas por meio de uma instância de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="2393d-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="2393d-639">O código dentro de `UseMvc` é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="2393d-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="2393d-640">`UseMvc` não define diretamente nenhuma rota, ele adiciona um espaço reservado à coleção de rotas para a rota `attribute`.</span><span class="sxs-lookup"><span data-stu-id="2393d-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="2393d-641">A sobrecarga `UseMvc(Action<IRouteBuilder>)` permite adicionar suas próprias rotas e também dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="2393d-642">`UseMvc`e todas as suas variações adicionam um espaço reservado para o atributo roteamento de atributo de rota está sempre disponível, independentemente `UseMvc`de como você configura.</span><span class="sxs-lookup"><span data-stu-id="2393d-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="2393d-643">`UseMvcWithDefaultRoute` define uma rota padrão e dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="2393d-644">A seção [Roteamento de atributos](#attribute-routing-ref-label) inclui mais detalhes sobre o roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="2393d-645">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="2393d-645">Conventional routing</span></span>

<span data-ttu-id="2393d-646">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="2393d-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2393d-647">O código anterior é um exemplo de roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="2393d-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="2393d-648">Esse estilo é chamado de roteamento convencional porque ele estabelece uma *Convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="2393d-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="2393d-649">O primeiro segmento de caminho mapeia para o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="2393d-650">O segundo mapeia para o nome da ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-650">The second maps to the action name.</span></span>
* <span data-ttu-id="2393d-651">O terceiro segmento é usado para um opcional `id`.</span><span class="sxs-lookup"><span data-stu-id="2393d-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="2393d-652">`id`mapeia para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="2393d-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="2393d-653">Usando essa rota `default`, o caminho da URL `/Products/List` é mapeado para a ação `ProductsController.List` e `/Blog/Article/17` é mapeado para `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="2393d-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="2393d-654">Esse mapeamento é baseado **somente** nos nomes do controlador e da ação e não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="2393d-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="2393d-655">Usar o roteamento convencional com a rota padrão permite compilar o aplicativo rapidamente sem precisar criar um novo padrão de URL para cada ação que você definir.</span><span class="sxs-lookup"><span data-stu-id="2393d-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="2393d-656">Para um aplicativo com ações de estilo CRUD, ter consistência para as URLs em seus controladores pode ajudar a simplificar seu código e a tornar sua interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="2393d-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="2393d-657">O `id` é definido como opcional pelo modelo de rota, o que significa que suas ações podem ser executadas sem a ID fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="2393d-658">Normalmente, o que acontecerá se `id` for omitido da URL é que ele será definido como `0` pelo model binding e, dessa forma, não será encontrada no banco de dados nenhuma entidade correspondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="2393d-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="2393d-659">O roteamento de atributos pode lhe proporcionar controle refinado para tornar a ID obrigatória para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="2393d-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="2393d-660">Por convenção, a documentação incluirá parâmetros opcionais, como `id`, quando for provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="2393d-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="2393d-661">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="2393d-661">Multiple routes</span></span>

<span data-ttu-id="2393d-662">É possível adicionar várias rotas dentro de `UseMvc` adicionando mais chamadas para `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="2393d-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="2393d-663">Fazer isso permite que você defina várias convenções ou que adicione rotas convencionais dedicadas a uma ação específica, como:</span><span class="sxs-lookup"><span data-stu-id="2393d-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2393d-664">A rota `blog` aqui é uma *rota convencional dedicada*, o que significa que ela usa o sistema de roteamento convencional, mas é dedicada a uma ação específica.</span><span class="sxs-lookup"><span data-stu-id="2393d-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="2393d-665">Como `controller` e `action` não aparecem no modelo de rota como parâmetros, eles só podem ter os valores padrão e, portanto, essa rota sempre será mapeada para a ação `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="2393d-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="2393d-666">As rotas na coleção de rotas são ordenadas e serão processadas na ordem em que forem adicionadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="2393d-667">Portanto, neste exemplo, a rota `blog` será tentada antes da rota `default`.</span><span class="sxs-lookup"><span data-stu-id="2393d-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-668">*Rotas convencionais dedicadas* geralmente usam parâmetros de rota **catch-all** como `{*article}` para capturar a parte restante do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="2393d-669">Isso pode fazer com que uma rota fique "muito ambiciosa", ou seja, que faça a correspondência com URLs que deveriam ser correspondidas com outras rotas.</span><span class="sxs-lookup"><span data-stu-id="2393d-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="2393d-670">Coloque as rotas "ambiciosas" mais adiante na tabela de rotas para solucionar esse problema.</span><span class="sxs-lookup"><span data-stu-id="2393d-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="2393d-671">Fallback</span><span class="sxs-lookup"><span data-stu-id="2393d-671">Fallback</span></span>

<span data-ttu-id="2393d-672">Como parte do processamento de solicitações, o MVC verificará se o valores das rotas podem ser usados para encontrar um controlador e uma ação em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="2393d-673">Se os valores das rotas não corresponderem a uma ação, a rota não será considerada correspondente e a próxima rota será tentada.</span><span class="sxs-lookup"><span data-stu-id="2393d-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="2393d-674">Isso é chamado de *fallback* e sua finalidade é simplificar casos em que rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="2393d-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="2393d-675">Desambiguação de ações</span><span class="sxs-lookup"><span data-stu-id="2393d-675">Disambiguating actions</span></span>

<span data-ttu-id="2393d-676">Quando duas ações correspondem por meio do roteamento, o MVC precisa resolver a ambiguidade para escolher a "melhor" candidata ou lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="2393d-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="2393d-677">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="2393d-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="2393d-678">Esse controlador define duas ações que fariam a correspondência entre caminho da URL `/Products/Edit/17` e a os dados da rota `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="2393d-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="2393d-679">Este é um padrão comum para controladores MVC em que `Edit(int)` mostra um formulário para editar um produto e `Edit(int, Product)` processa o formulário postado.</span><span class="sxs-lookup"><span data-stu-id="2393d-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="2393d-680">Para que isso seja possível, o MVC precisa escolher `Edit(int, Product)` quando a solicitação é um `POST` HTTP e `Edit(int)` quando o verbo HTTP é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="2393d-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="2393d-681">O `HttpPostAttribute` (`[HttpPost]`) é uma implementação de `IActionConstraint` que só permite que a ação seja selecionada quando o verbo HTTP é `POST`.</span><span class="sxs-lookup"><span data-stu-id="2393d-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="2393d-682">A presença de um `IActionConstraint` faz do `Edit(int, Product)` uma "melhor" correspondência do que `Edit(int)`, portanto, `Edit(int, Product)` será tentado primeiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="2393d-683">Você só precisará gravar implementações personalizadas de `IActionConstraint` em cenários especializados, mas é importante compreender a função de atributos como `HttpPostAttribute` – atributos semelhantes são definidos para outros verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="2393d-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="2393d-684">No roteamento convencional, é comum que ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="2393d-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="2393d-685">A conveniência desse padrão ficará mais aparente após você revisar a seção [Noções básicas sobre IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="2393d-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="2393d-686">Se várias rotas corresponderem e o MVC não puder encontrar uma rota "melhor", ele gerará um `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="2393d-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="2393d-687">Nomes de rotas</span><span class="sxs-lookup"><span data-stu-id="2393d-687">Route names</span></span>

<span data-ttu-id="2393d-688">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas:</span><span class="sxs-lookup"><span data-stu-id="2393d-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2393d-689">Os nomes de rotas dão a uma rota um nome lógico, de modo que a rota nomeada possa ser usada para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="2393d-690">Isso simplifica muito a criação de URLs quando a ordenação de rotas poderia complicá-la.</span><span class="sxs-lookup"><span data-stu-id="2393d-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="2393d-691">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="2393d-692">Os nomes de rotas não têm impacto sobre a correspondência de URLs ou o tratamento de solicitações; eles são usados apenas para a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="2393d-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="2393d-693">[Roteamento](xref:fundamentals/routing) tem informações mais detalhadas sobre geração de URLs, incluindo a geração de URLs em auxiliares específicos do MVC.</span><span class="sxs-lookup"><span data-stu-id="2393d-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="2393d-694">Roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-694">Attribute routing</span></span>

<span data-ttu-id="2393d-695">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="2393d-696">No exemplo a seguir, `app.UseMvc();` é usado no método `Configure` e nenhuma rota é passada.</span><span class="sxs-lookup"><span data-stu-id="2393d-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="2393d-697">O `HomeController` corresponderá a um conjunto de URLs semelhantes ao que a rota padrão `{controller=Home}/{action=Index}/{id?}` corresponderia:</span><span class="sxs-lookup"><span data-stu-id="2393d-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="2393d-698">A ação `HomeController.Index()` será executada para qualquer um dos caminhos de URL `/`, `/Home` ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="2393d-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-699">Este exemplo destaca uma diferença importante de programação entre o roteamento de atributo e o roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="2393d-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="2393d-700">O roteamento de atributo requer mais entradas para especificar uma rota; a rota padrão convencional manipula as rotas de forma mais sucinta.</span><span class="sxs-lookup"><span data-stu-id="2393d-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="2393d-701">No entanto, o roteamento de atributo permite (e exige) o controle preciso de quais modelos de rota se aplicam a cada ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="2393d-702">Com o roteamento de atributo, o nome do controlador e os nomes de ação não desempenham **nenhuma** função quanto a qual ação é selecionada.</span><span class="sxs-lookup"><span data-stu-id="2393d-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="2393d-703">Este exemplo corresponderá as mesmas URLs que o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="2393d-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="2393d-704">Os modelos de rota acima não definem parâmetros de rota para `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="2393d-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="2393d-705">Na verdade, esses parâmetros de rota não são permitidos em rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="2393d-706">Uma vez que o modelo de rota já está associado a uma ação, não faria sentido analisar o nome da ação da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="2393d-707">Roteamento de atributo com atributos Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="2393d-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="2393d-708">O roteamento de atributo também pode usar atributos `Http[Verb]`, como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="2393d-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="2393d-709">Todos esses atributos podem aceitar um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="2393d-710">Este exemplo mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="2393d-711">Para um caminho de URL como `/products`, a ação `ProductsApi.ListProducts` será executada quando o verbo HTTP for `GET` e `ProductsApi.CreateProduct` será executado quando o verbo HTTP for `POST`.</span><span class="sxs-lookup"><span data-stu-id="2393d-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="2393d-712">Primeiro, o roteamento de atributo faz a correspondência da URL com o conjunto de modelos de rota definidos por atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="2393d-713">Quando um modelo de rota for correspondente, restrições de `IActionConstraint` serão aplicadas para determinar quais ações podem ser executadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="2393d-714">Ao criar uma API REST, é raro que você queira usar `[Route(...)]` em um método de ação, pois a ação aceitará todos os métodos http.</span><span class="sxs-lookup"><span data-stu-id="2393d-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="2393d-715">É melhor usar o `Http*Verb*Attributes` mais específico para ser preciso quanto ao que tem suporte de sua API.</span><span class="sxs-lookup"><span data-stu-id="2393d-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="2393d-716">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="2393d-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="2393d-717">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="2393d-718">Neste exemplo, `id` é obrigatório como parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2393d-719">A ação `ProductsApi.GetProduct(int)` será executada para um caminho de URL como `/products/3`, mas não para um caminho de URL como `/products`.</span><span class="sxs-lookup"><span data-stu-id="2393d-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="2393d-720">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="2393d-721">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="2393d-721">Route Name</span></span>

<span data-ttu-id="2393d-722">O código a seguir define um *nome da rota* como `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="2393d-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2393d-723">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="2393d-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="2393d-724">Nomes de rota não têm impacto sobre o comportamento de correspondência da URL e são usados somente para geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="2393d-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="2393d-725">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-726">Compare isso com a *rota padrão* convencional, que define o parâmetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="2393d-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="2393d-727">Essa capacidade de especificar APIs de forma específica tem vantagens, como permitir que `/products` e `/products/5` sejam expedidos para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="2393d-728">Combinando rotas</span><span class="sxs-lookup"><span data-stu-id="2393d-728">Combining routes</span></span>

<span data-ttu-id="2393d-729">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="2393d-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="2393d-730">Modelos de rota definidos no controlador precedem modelos de rota nas ações. </span><span class="sxs-lookup"><span data-stu-id="2393d-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="2393d-731">Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="2393d-732">Neste exemplo, o caminho de URL `/products` pode corresponder a `ProductsApi.ListProducts` e o caminho de URL `/products/5` pode corresponder a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="2393d-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="2393d-733">Ambas as ações correspondem apenas a HTTP `GET` porque elas são marcadas com `HttpGetAttribute`o.</span><span class="sxs-lookup"><span data-stu-id="2393d-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="2393d-734">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="2393d-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="2393d-735">Este exemplo corresponde a um conjunto de caminhos de URL semelhante à *rota padrão*.</span><span class="sxs-lookup"><span data-stu-id="2393d-735">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="2393d-736">Ordenando rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="2393d-736">Ordering attribute routes</span></span>

<span data-ttu-id="2393d-737">Ao contrário das rotas convencionais, que são executadas em uma ordem definida, o roteamento de atributo cria uma árvore e corresponde a todas as rotas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="2393d-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="2393d-738">O comportamento é como se as entradas de rota fossem colocadas em uma ordem ideal; as rotas mais específicas têm uma chance de ser executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="2393d-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="2393d-739">Por exemplo, uma rota como `blog/search/{topic}` é mais específica que uma rota como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="2393d-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="2393d-740">Em termos da lógica, a rota `blog/search/{topic}` é "executada" primeiro, por padrão, porque essa é a única ordem que faz sentido.</span><span class="sxs-lookup"><span data-stu-id="2393d-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="2393d-741">Usando o roteamento convencional, o desenvolvedor é responsável por colocar as rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="2393d-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="2393d-742">Rotas de atributos podem configurar uma ordem, usando a propriedade `Order` de todos os atributos de rota fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="2393d-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="2393d-743">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="2393d-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="2393d-744">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="2393d-744">The default order is `0`.</span></span> <span data-ttu-id="2393d-745">Uma rota definida usando `Order = -1` será executada antes de rotas que não definem uma ordem.</span><span class="sxs-lookup"><span data-stu-id="2393d-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="2393d-746">Uma rota definida usando `Order = 1` será executada após a ordem das rotas padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="2393d-747">Evite depender de `Order`.</span><span class="sxs-lookup"><span data-stu-id="2393d-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="2393d-748">Se o seu espaço de URL exigir valores de ordem explícita para fazer o roteamento corretamente, provavelmente ele também será confuso para os clientes.</span><span class="sxs-lookup"><span data-stu-id="2393d-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="2393d-749">De modo geral, o roteamento de atributos selecionará a rota correta com a correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="2393d-750">Se a ordem padrão usada para a geração de URL não estiver funcionando, usar o nome da rota como uma substituição geralmente será mais simples do que aplicar a propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="2393d-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="2393d-751">O roteamento de páginas e o roteamento do controlador MVC compartilham uma implementação.</span><span class="sxs-lookup"><span data-stu-id="2393d-751"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="2393d-752">As informações sobre a ordem de Razor rota nos tópicos de páginas estão disponíveis em [ Razor páginas rota e convenções de aplicativo: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="2393d-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="2393d-753">Substituição de token em modelos de rota ([controlador] [ação] [área])</span><span class="sxs-lookup"><span data-stu-id="2393d-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="2393d-754">Para sua conveniência, as rotas de atributo dão suporte à *substituição de token* ao colocar um token entre`[`colchetes (, `]`).</span><span class="sxs-lookup"><span data-stu-id="2393d-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="2393d-755">Os tokens `[action]`, `[area]` e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida.</span><span class="sxs-lookup"><span data-stu-id="2393d-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="2393d-756">No exemplo a seguir, as ações correspondem a caminhos de URL conforme descrito nos comentários:</span><span class="sxs-lookup"><span data-stu-id="2393d-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="2393d-757">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="2393d-758">O exemplo acima se comportará da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="2393d-759">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="2393d-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="2393d-760">Isso é especialmente eficiente em combinação com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="2393d-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="2393d-761">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="2393d-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="2393d-763">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="2393d-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="2393d-764">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="2393d-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="2393d-765">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="2393d-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="2393d-766">Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="2393d-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="2393d-767">Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="2393d-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="2393d-768">O `RouteTokenTransformerConvention` é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="2393d-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="2393d-769">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="2393d-770">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="2393d-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="2393d-771">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2393d-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="2393d-772">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="2393d-772">Multiple Routes</span></span>

<span data-ttu-id="2393d-773">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="2393d-774">O uso mais comum desse recurso é para simular o comportamento da *rota convencional padrão*, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="2393d-775">Colocar vários atributos de rota no controlador significa que cada um deles será combinado com cada um dos atributos de rota nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="2393d-776">Quando vários atributos de rota (que implementam `IActionConstraint`) são colocados em uma ação, cada restrição da ação combina com o modelo de rota do atributo que a definiu.</span><span class="sxs-lookup"><span data-stu-id="2393d-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="2393d-777">Embora o uso de várias rotas em ações possa parecer eficaz, é melhor manter o espaço de URL de seu aplicativo simples e bem definido.</span><span class="sxs-lookup"><span data-stu-id="2393d-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="2393d-778">Use várias rotas em ações somente quando for necessário; por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="2393d-779">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="2393d-780">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="2393d-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="2393d-781">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="2393d-782">Atributos de rota personalizados usando `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="2393d-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="2393d-783">Todos os atributos de rota fornecidos na estrutura ( `[Route(...)]`, `[HttpGet(...)]` etc.) implementam a interface `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="2393d-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="2393d-784">O MVC procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado e usa aqueles que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="2393d-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="2393d-785">Você pode implementar `IRouteTemplateProvider` para definir seus próprios atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="2393d-786">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="2393d-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="2393d-787">O atributo do exemplo acima configura automaticamente o `Template` como `"api/[controller]"` quando `[MyApiController]` é aplicado.</span><span class="sxs-lookup"><span data-stu-id="2393d-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="2393d-788">Usando o Modelo de Aplicativo para personalizar rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="2393d-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="2393d-789">O *modelo de aplicativo* é um modelo de objeto criado durante a inicialização com todos os metadados usados pelo MVC para rotear e executar suas ações.</span><span class="sxs-lookup"><span data-stu-id="2393d-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="2393d-790">O *modelo de aplicativo* inclui todos os dados reunidos dos atributos de rota (por meio de `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="2393d-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="2393d-791">Você pode escrever *convenções* para modificar o modelo do aplicativo no momento da inicialização para personalizar o comportamento do roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="2393d-792">Esta seção mostra um exemplo simples de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2393d-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="2393d-793">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="2393d-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="2393d-794">Aplicativos MVC podem combinar o uso do roteamento convencional e do roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="2393d-795">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="2393d-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="2393d-796">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="2393d-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="2393d-797">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="2393d-798">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="2393d-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="2393d-799">**Qualquer** atributo de rota no controlador faz com que todas as ações no atributo de controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-800">O que diferencia os dois tipos de sistemas de roteamento é o processo aplicado após uma URL corresponder a um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="2393d-801">No roteamento convencional, os valores de rota da correspondência são usados para escolher a ação e o controlador em uma tabela de pesquisa com todas as ações roteadas convencionais.</span><span class="sxs-lookup"><span data-stu-id="2393d-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="2393d-802">No roteamento de atributo, cada modelo já está associado a uma ação e nenhuma pesquisa adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="2393d-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="2393d-803">Segmentos complexos</span><span class="sxs-lookup"><span data-stu-id="2393d-803">Complex segments</span></span>

<span data-ttu-id="2393d-804">Segmentos complexos (por exemplo, `[Route("/dog{token}cat")]`), são processados combinando literais da direita para a esquerda de uma maneira não Greedy.</span><span class="sxs-lookup"><span data-stu-id="2393d-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="2393d-805">Veja [o código-fonte](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) para obter uma descrição.</span><span class="sxs-lookup"><span data-stu-id="2393d-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="2393d-806">Para obter mais informações, confira [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="2393d-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="2393d-807">Geração de URL</span><span class="sxs-lookup"><span data-stu-id="2393d-807">URL Generation</span></span>

<span data-ttu-id="2393d-808">Aplicativos MVC podem usar os recursos de geração de URL do roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="2393d-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="2393d-809">Gerar URLs elimina a necessidade de codificar URLs, tornando seu código mais robusto e sustentável.</span><span class="sxs-lookup"><span data-stu-id="2393d-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="2393d-810">Esta seção tem como foco os recursos de geração de URL fornecidos pelo MVC e só aborda as noções básicas de como a geração de URL funciona.</span><span class="sxs-lookup"><span data-stu-id="2393d-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="2393d-811">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="2393d-812">A interface `IUrlHelper` é a parte subjacente da infraestrutura entre o MVC e o roteamento para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="2393d-813">Você encontrará uma instância de `IUrlHelper` disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="2393d-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="2393d-814">Neste exemplo, a interface `IUrlHelper` é usada por meio a propriedade `Controller.Url` para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="2393d-815">Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho de URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="2393d-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="2393d-816">Esse caminho de URL é criado pelo roteamento combinando os valores de rota da solicitação atual (valores de ambiente) com os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="2393d-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="2393d-817">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2393d-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="2393d-818">Um parâmetro de rota que não tem um valor pode usar um valor padrão se houver um ou pode ser ignorado se for opcional (como no caso de `id` neste exemplo).</span><span class="sxs-lookup"><span data-stu-id="2393d-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="2393d-819">A geração de URL falhará se qualquer parâmetro de rota obrigatório não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="2393d-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="2393d-820">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="2393d-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="2393d-821">O exemplo de `Url.Action` acima pressupõe que o roteamento seja convencional, mas a geração de URL funciona de forma semelhante com o roteamento de atributo, embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="2393d-822">Com o roteamento convencional, os valores de rota são usados para expandir um modelo e os valores de rota para `controller` e `action` normalmente são exibidos no modelo – isso funciona porque as URLs correspondidas pelo roteamento aderem a uma *convenção*.</span><span class="sxs-lookup"><span data-stu-id="2393d-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="2393d-823">No roteamento de atributo, os valores de rota para `controller` e `action` não podem ser exibidos no modelo; em vez disso, eles são usados para pesquisar o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="2393d-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="2393d-824">Este exemplo usa o roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="2393d-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="2393d-825">O MVC cria uma tabela de pesquisa de todas as ações de atributo roteadas e faz a correspondência dos valores de `controller` e `action` para selecionar o modelo de rota a ser usado para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="2393d-826">Na amostra acima, `custom/url/to/destination` é gerado.</span><span class="sxs-lookup"><span data-stu-id="2393d-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="2393d-827">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="2393d-827">Generating URLs by action name</span></span>

<span data-ttu-id="2393d-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="2393d-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="2393d-829">`Action`) e todas as sobrecargas relacionadas são baseadas na ideia de que você deseja especificar ao que está vinculando, especificando um nome do controlador e um nome da ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-830">Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são especificados para você – o valor de `controller` e `action` fazem parte de *valores de ambiente* \*\*e de \*\* *valores*.</span><span class="sxs-lookup"><span data-stu-id="2393d-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="2393d-831">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.</span><span class="sxs-lookup"><span data-stu-id="2393d-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="2393d-832">O roteamento tenta usar os valores em valores de ambiente para preencher informações que você não forneceu ao gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="2393d-833">Usando uma rota como `{a}/{b}/{c}/{d}` e valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, o roteamento tem informações suficientes para gerar uma URL sem valores adicionais – uma vez que todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="2393d-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="2393d-834">Se você tiver adicionado o valor `{ d = Donovan }`, o valor `{ d = David }` será ignorado e o caminho de URL gerado será `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="2393d-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="2393d-835">Caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="2393d-835">URL paths are hierarchical.</span></span> <span data-ttu-id="2393d-836">No exemplo acima, se você tiver adicionado o valor `{ c = Cheryl }`, ambos os valores `{ c = Carol, d = David }` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="2393d-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="2393d-837">Nesse caso, não teremos mais um valor para `d` e a geração de URL falhará.</span><span class="sxs-lookup"><span data-stu-id="2393d-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="2393d-838">Você precisaria especificar o valor desejado de `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="2393d-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="2393d-839">Você pode esperar se deparar com esse problema com a rota padrão (`{controller}/{action}/{id?}`) – mas raramente encontrará esse comportamento na prática, pois `Url.Action` sempre especificará explicitamente um valor de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="2393d-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="2393d-840">Sobrecargas maiores de `Url.Action` também usam um objeto adicional de *valores de rota* para fornecer valores para parâmetros de rota diferentes de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="2393d-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="2393d-841">É mais comum ver isso com `id` como `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="2393d-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="2393d-842">Por convenção, o objeto de *valores de rota* geralmente é um objeto de tipo anônimo, mas também pode ser um `IDictionary<>` ou um *objeto .NET simples*.</span><span class="sxs-lookup"><span data-stu-id="2393d-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="2393d-843">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="2393d-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="2393d-844">Para criar uma URL absoluta, use uma sobrecarga que aceita um `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="2393d-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="2393d-845">Gerando URLs pela rota</span><span class="sxs-lookup"><span data-stu-id="2393d-845">Generating URLs by route</span></span>

<span data-ttu-id="2393d-846">O código acima demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="2393d-847">`IUrlHelper` também fornece a família de métodos `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="2393d-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="2393d-848">Esses métodos são semelhantes a `Url.Action`, mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="2393d-849">O uso mais comum é especificar um nome de rota para usar uma rota específica para gerar a URL, geralmente *sem* especificar um nome de controlador ou de ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="2393d-850">Gerar URLs em HTML</span><span class="sxs-lookup"><span data-stu-id="2393d-850">Generating URLs in HTML</span></span>

<span data-ttu-id="2393d-851">`IHtmlHelper` fornece o métodos `Html.BeginForm` e `Html.ActionLink` de `HtmlHelper` para gerar elementos `<form>` e `<a>` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="2393d-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="2393d-852">Esses métodos usam o método `Url.Action` para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="2393d-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="2393d-853">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="2393d-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="2393d-854">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="2393d-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="2393d-855">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="2393d-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="2393d-856">Consulte [Trabalhando com Formulários](../views/working-with-forms.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="2393d-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="2393d-857">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="2393d-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="2393d-858">Gerando URLS nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="2393d-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="2393d-859">Os exemplos acima mostraram o uso de `IUrlHelper` em um controlador, enquanto o uso mais comum em um controlador é gerar uma URL como parte do resultado de uma ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="2393d-860">As classes base `ControllerBase` e `Controller` fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="2393d-861">Um uso típico é para redirecionar após aceitar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="2393d-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="2393d-862">Os métodos de fábrica dos resultados da ação seguem um padrão semelhante aos métodos em `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="2393d-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="2393d-863">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="2393d-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="2393d-864">O roteamento convencional pode usar um tipo especial de definição de rota chamado *rota convencional dedicada*.</span><span class="sxs-lookup"><span data-stu-id="2393d-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="2393d-865">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada.</span><span class="sxs-lookup"><span data-stu-id="2393d-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="2393d-866">Usando essas definições de rota, `Url.Action("Index", "Home")` gerará o caminho de URL `/` com a rota `default`, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="2393d-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="2393d-867">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="2393d-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="2393d-868">Rotas convencionais dedicadas dependem de um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impeça que a rota seja "muito ambiciosa" com a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="2393d-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="2393d-869">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="2393d-870">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="2393d-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="2393d-871">A geração de URL usando `blog` falhará porque os valores de `{ controller = Home, action = Index }` não correspondem a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="2393d-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="2393d-872">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="2393d-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="2393d-873">Áreas</span><span class="sxs-lookup"><span data-stu-id="2393d-873">Areas</span></span>

<span data-ttu-id="2393d-874">[Áreas](areas.md) são um recurso do MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace de roteamento (para ações do controlador) e estrutura de pasta (para exibições) separada.</span><span class="sxs-lookup"><span data-stu-id="2393d-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="2393d-875">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="2393d-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="2393d-876">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="2393d-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="2393d-877">Esta seção aborda como o roteamento interage com as áreas. Consulte [Áreas](areas.md) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="2393d-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="2393d-878">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma *rota de área* para uma área chamada `Blog`:</span><span class="sxs-lookup"><span data-stu-id="2393d-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="2393d-879">Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a primeira rota produzirá os valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="2393d-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="2393d-880">O valor de rota `area` é produzido por um valor padrão para `area`. De fato, a rota criada por `MapAreaRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="2393d-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="2393d-881">`MapAreaRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2393d-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="2393d-882">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="2393d-883">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="2393d-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="2393d-884">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="2393d-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="2393d-885">Usando o exemplo acima, os valores de rota corresponderiam à ação a seguir:</span><span class="sxs-lookup"><span data-stu-id="2393d-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="2393d-886">O `AreaAttribute` é o que indica que um controlador faz parte de uma área; dizemos que esse controlador está na área `Blog`.</span><span class="sxs-lookup"><span data-stu-id="2393d-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="2393d-887">Controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** corresponderão quando o valor de rota `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="2393d-888">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="2393d-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="2393d-889">O namespace de cada controlador é mostrado aqui para fins de integridade – caso contrário, os controladores teriam um conflito de nomenclatura e gerariam um erro do compilador.</span><span class="sxs-lookup"><span data-stu-id="2393d-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="2393d-890">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="2393d-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="2393d-891">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="2393d-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="2393d-892">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="2393d-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-893">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="2393d-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="2393d-894">Ao executar uma ação dentro de uma área, o valor de rota para `area` estará disponível como um *valor de ambiente* para o roteamento usar para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="2393d-895">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="2393d-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="2393d-896">Entendendo IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="2393d-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="2393d-897">Esta seção é uma análise aprofundada dos elementos internos da estrutura e de como o MVC escolhe uma ação para ser executada.</span><span class="sxs-lookup"><span data-stu-id="2393d-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="2393d-898">Um aplicativo típico não precisará de um `IActionConstraint` personalizado</span><span class="sxs-lookup"><span data-stu-id="2393d-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="2393d-899">Provavelmente, você já usou `IActionConstraint` mesmo que não esteja familiarizado com a interface.</span><span class="sxs-lookup"><span data-stu-id="2393d-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="2393d-900">O atributo `[HttpGet]` e atributos `[Http-VERB]` semelhantes implementam `IActionConstraint` para limitar a execução de um método de ação.</span><span class="sxs-lookup"><span data-stu-id="2393d-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="2393d-901">Presumindo a rota convencional padrão, o caminho de URL `/Products/Edit` produziria os valores `{ controller = Products, action = Edit }`, que corresponderiam a **ambas** as ações mostradas aqui.</span><span class="sxs-lookup"><span data-stu-id="2393d-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="2393d-902">Na terminologia `IActionConstraint`, diríamos que essas duas ações são consideradas candidatas – uma vez que ambas correspondem aos dados da rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="2393d-903">Quando for executado, `HttpGetAttribute` indicará que *Edit()* corresponde a *GET* e não corresponde a nenhum outro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="2393d-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="2393d-904">A ação `Edit(...)` não tem restrições definidas e, portanto, corresponderá a qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="2393d-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="2393d-905">Sendo assim, supondo um `POST`, `Edit(...)` será correspondente.</span><span class="sxs-lookup"><span data-stu-id="2393d-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="2393d-906">Mas, para um `GET`, ambas as ações ainda podem corresponder – no entanto, uma ação com um `IActionConstraint` sempre é considerada *melhor* que uma ação sem.</span><span class="sxs-lookup"><span data-stu-id="2393d-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="2393d-907">Assim, como `Edit()` tem `[HttpGet]`, ela é considerada mais específica e será selecionada se as duas ações puderem corresponder.</span><span class="sxs-lookup"><span data-stu-id="2393d-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="2393d-908">Conceitualmente, `IActionConstraint` é uma forma de *sobrecarga*, mas em vez de uma sobrecarga de métodos com o mesmo nome, trata-se da sobrecarga entre ações que correspondem à mesma URL.</span><span class="sxs-lookup"><span data-stu-id="2393d-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="2393d-909">O roteamento de atributo também usa `IActionConstraint` e pode fazer com que ações de controladores diferentes sejam consideradas candidatas.</span><span class="sxs-lookup"><span data-stu-id="2393d-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="2393d-910">Implementando IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="2393d-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="2393d-911">A maneira mais simples de implementar um `IActionConstraint` é criar uma classe derivada de `System.Attribute` e colocá-la em suas ações e controladores.</span><span class="sxs-lookup"><span data-stu-id="2393d-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="2393d-912">O MVC descobrirá automaticamente qualquer `IActionConstraint` que for aplicado como atributo.</span><span class="sxs-lookup"><span data-stu-id="2393d-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="2393d-913">Você pode usar o modelo de aplicativo para aplicar restrições e, provavelmente, essa é a abordagem mais flexível, pois permite a você faça uma metaprogramação de como elas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="2393d-914">No exemplo a seguir, uma restrição escolhe uma ação com base em um *código de país* dos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="2393d-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="2393d-915">O [exemplo completo no GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="2393d-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="2393d-916">Você é responsável por implementar o método `Accept` e por escolher uma "ordem" na qual a restrição deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="2393d-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="2393d-917">Nesse caso, o método `Accept` retorna `true` para indicar que a ação é correspondente quando o valor de rota `country` é correspondente.</span><span class="sxs-lookup"><span data-stu-id="2393d-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="2393d-918">Isso é diferente de um `RouteValueAttribute`, pois permite o fallback para uma ação não atribuída.</span><span class="sxs-lookup"><span data-stu-id="2393d-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="2393d-919">O exemplo mostra que se você definir uma ação `en-US`, um código de país como `fr-FR` fará o fallback para um controlador mais genérico que não tem `[CountrySpecific(...)]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="2393d-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="2393d-920">A propriedade `Order` decide de qual *estágio* a restrição faz parte.</span><span class="sxs-lookup"><span data-stu-id="2393d-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="2393d-921">Restrições de ação são executadas em grupos com base no `Order`.</span><span class="sxs-lookup"><span data-stu-id="2393d-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="2393d-922">Por exemplo, todos atributos de método HTTP fornecidos pela estrutura usam o mesmo valor de `Order` para que sejam executados no mesmo estágio.</span><span class="sxs-lookup"><span data-stu-id="2393d-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="2393d-923">Você pode ter tantos estágios quantos forem necessários para implementar suas políticas desejadas.</span><span class="sxs-lookup"><span data-stu-id="2393d-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="2393d-924">Para decidir o valor para `Order`, considere se sua restrição deve ou não deve ser aplicada antes de métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="2393d-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="2393d-925">Números inferiores são executados primeiro.</span><span class="sxs-lookup"><span data-stu-id="2393d-925">Lower numbers run first.</span></span>

::: moniker-end
