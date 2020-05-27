---
<span data-ttu-id="01d5e-101">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-102">'Blazor'</span></span>
- <span data-ttu-id="01d5e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-103">'Identity'</span></span>
- <span data-ttu-id="01d5e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-105">'Razor'</span></span>
- <span data-ttu-id="01d5e-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="01d5e-107">Roteamento para ações do controlador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01d5e-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="01d5e-108">Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01d5e-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01d5e-109">Os controladores de ASP.NET Core usam o [middleware](xref:fundamentals/middleware/index) de roteamento para corresponder as URLs de solicitações de entrada e mapeá-las para [ações](#action).</span><span class="sxs-lookup"><span data-stu-id="01d5e-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="01d5e-110">Modelos de rotas:</span><span class="sxs-lookup"><span data-stu-id="01d5e-110">Routes templates:</span></span>

* <span data-ttu-id="01d5e-111">São definidos em código ou atributos de inicialização.</span><span class="sxs-lookup"><span data-stu-id="01d5e-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="01d5e-112">Descreva como os caminhos de URL são correspondidos às [ações](#action).</span><span class="sxs-lookup"><span data-stu-id="01d5e-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="01d5e-113">São usados para gerar URLs para links.</span><span class="sxs-lookup"><span data-stu-id="01d5e-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="01d5e-114">Os links gerados são normalmente retornados em respostas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="01d5e-115">As ações são [roteadas por convenção](#cr) ou [roteadas por atributo](#ar).</span><span class="sxs-lookup"><span data-stu-id="01d5e-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="01d5e-116">Colocar uma rota no controlador ou na [ação](#action) a torna roteada por atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="01d5e-117">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="01d5e-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="01d5e-118">Este documento:</span><span class="sxs-lookup"><span data-stu-id="01d5e-118">This document:</span></span>

* <span data-ttu-id="01d5e-119">Explica as interações entre o MVC e o roteamento:</span><span class="sxs-lookup"><span data-stu-id="01d5e-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="01d5e-120">Como os aplicativos MVC típicos fazem uso dos recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="01d5e-121">Aborda ambos:</span><span class="sxs-lookup"><span data-stu-id="01d5e-121">Covers both:</span></span>
    * <span data-ttu-id="01d5e-122">[Roteamento convencional](#cr) normalmente usado com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="01d5e-123">*Roteamento de atributos* usado com APIs REST.</span><span class="sxs-lookup"><span data-stu-id="01d5e-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="01d5e-124">Se você estiver interessado principalmente no roteamento de APIs REST, vá para a seção [Roteamento de atributo para APIs REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="01d5e-125">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes avançados de roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="01d5e-126">Refere-se ao sistema de roteamento padrão adicionado no ASP.NET Core 3,0, chamado roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="01d5e-127">É possível usar controladores com a versão anterior do roteamento para fins de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="01d5e-128">Consulte o [Guia de migração 2.2-3.0](xref:migration/22-to-30) para obter instruções.</span><span class="sxs-lookup"><span data-stu-id="01d5e-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="01d5e-129">Consulte a [versão 2,2 deste documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obter material de referência sobre o sistema de roteamento herdado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="01d5e-130">Configurar a rota convencional</span><span class="sxs-lookup"><span data-stu-id="01d5e-130">Set up conventional route</span></span>

<span data-ttu-id="01d5e-131">`Startup.Configure`geralmente tem um código semelhante ao seguinte ao usar o [Roteamento convencional](#crd):</span><span class="sxs-lookup"><span data-stu-id="01d5e-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="01d5e-132">Dentro da chamada para <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> é usado para criar uma única rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="01d5e-133">A rota única é chamada `default` rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-133">The single route is named `default` route.</span></span> <span data-ttu-id="01d5e-134">A maioria dos aplicativos com controladores e exibições usa um modelo de rota semelhante à `default` rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="01d5e-135">As APIs REST devem usar o [Roteamento de atributos](#ar).</span><span class="sxs-lookup"><span data-stu-id="01d5e-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="01d5e-136">O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="01d5e-137">Corresponde a um caminho de URL como`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="01d5e-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="01d5e-138">Extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` por tokening do caminho.</span><span class="sxs-lookup"><span data-stu-id="01d5e-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="01d5e-139">A extração dos valores de rota resultará em uma correspondência se o aplicativo tiver um controlador chamado `ProductsController` e uma `Details` ação:</span><span class="sxs-lookup"><span data-stu-id="01d5e-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="01d5e-140">`/Products/Details/5`o modelo associa o valor de `id = 5` para definir o `id` parâmetro como `5` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="01d5e-141">Consulte [Associação de modelo](xref:mvc/models/model-binding) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="01d5e-142">`{controller=Home}`define `Home` como o padrão `controller` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="01d5e-143">`{action=Index}`define `Index` como o padrão `action` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="01d5e-144">O `?` caractere em `{id?}` define `id` como opcional.</span><span class="sxs-lookup"><span data-stu-id="01d5e-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="01d5e-145">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="01d5e-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="01d5e-146">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="01d5e-147">Corresponde ao caminho da URL `/` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="01d5e-148">Produz os valores de rota `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="01d5e-149">Os valores para `controller` e `action` fazem uso dos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="01d5e-150">`id`não produz um valor, pois não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="01d5e-151">`/`corresponde somente se existir uma `HomeController` ação e `Index` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="01d5e-152">Usando a definição de controlador e o modelo de rota anteriores, a `HomeController.Index` ação é executada para os seguintes caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="01d5e-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="01d5e-153">O caminho da URL `/` usa a ação e os controladores padrão do modelo de rota `Home` `Index` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="01d5e-154">O caminho da URL `/Home` usa a ação padrão do modelo de rota `Index` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="01d5e-155">O método de conveniência <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="01d5e-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="01d5e-156">Substituto</span><span class="sxs-lookup"><span data-stu-id="01d5e-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="01d5e-157">O roteamento é configurado usando o <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> e o <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span><span class="sxs-lookup"><span data-stu-id="01d5e-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="01d5e-158">Para usar controladores:</span><span class="sxs-lookup"><span data-stu-id="01d5e-158">To use controllers:</span></span>
>
> * <span data-ttu-id="01d5e-159">Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> dentro `UseEndpoints` para mapear controladores [roteados de atributo](#ar) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="01d5e-160">Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , para mapear controladores [roteados de Convenção](#cr) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="01d5e-161">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="01d5e-161">Conventional routing</span></span>

<span data-ttu-id="01d5e-162">O roteamento convencional é usado com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="01d5e-163">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="01d5e-164">é um exemplo de *roteamento convencional*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="01d5e-165">Ele é chamado de *Roteamento convencional* porque estabelece uma *Convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="01d5e-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="01d5e-166">O primeiro segmento de caminho, `{controller=Home}` , é mapeado para o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="01d5e-167">O segundo segmento, `{action=Index}` , é mapeado para o nome da [ação](#action) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="01d5e-168">O terceiro segmento `{id?}` é usado para um opcional `id` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="01d5e-169">O `?` no `{id?}` torna opcional.</span><span class="sxs-lookup"><span data-stu-id="01d5e-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="01d5e-170">`id`é usado para mapear para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="01d5e-171">Usando essa `default` rota, o caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="01d5e-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="01d5e-172">`/Products/List`mapeia para a `ProductsController.List` ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="01d5e-173">`/Blog/Article/17`mapeia para `BlogController.Article` e normalmente vincula o parâmetro a `id` 17.</span><span class="sxs-lookup"><span data-stu-id="01d5e-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="01d5e-174">Este mapeamento:</span><span class="sxs-lookup"><span data-stu-id="01d5e-174">This mapping:</span></span>

* <span data-ttu-id="01d5e-175">É baseado **somente**nos nomes de controlador e de [ação](#action) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="01d5e-176">Não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="01d5e-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="01d5e-177">O uso do roteamento convencional com a rota padrão permite a criação do aplicativo sem a necessidade de surgir um novo padrão de URL para cada ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="01d5e-178">Para um aplicativo com ações de estilo [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , tendo consistência para as URLs entre controladores:</span><span class="sxs-lookup"><span data-stu-id="01d5e-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="01d5e-179">Ajuda a simplificar o código.</span><span class="sxs-lookup"><span data-stu-id="01d5e-179">Helps simplify the code.</span></span>
* <span data-ttu-id="01d5e-180">Torna a interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="01d5e-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="01d5e-181">O `id` no código anterior é definido como opcional pelo modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="01d5e-182">As ações podem ser executadas sem a ID opcional fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="01d5e-183">Em geral, quando `id` é omitido da URL:</span><span class="sxs-lookup"><span data-stu-id="01d5e-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="01d5e-184">`id`é definido como `0` por associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="01d5e-185">Nenhuma entidade encontrada na correspondência de banco de dados `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="01d5e-186">O [Roteamento de atributos](#ar) fornece controle refinado para tornar a ID necessária para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="01d5e-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="01d5e-187">Por convenção, a documentação inclui parâmetros opcionais como `id` quando é provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="01d5e-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="01d5e-188">A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="01d5e-189">A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="01d5e-190">Dá suporte a um esquema de roteamento básico e descritivo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="01d5e-191">É um ponto de partida útil para aplicativos baseados em interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="01d5e-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="01d5e-192">É o único modelo de rota necessário para muitos aplicativos da interface do usuário da Web.</span><span class="sxs-lookup"><span data-stu-id="01d5e-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="01d5e-193">Para aplicativos de interface do usuário da Web maiores, outra rota usando [áreas](#areas) , se for sempre tudo o que for necessário.</span><span class="sxs-lookup"><span data-stu-id="01d5e-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="01d5e-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="01d5e-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="01d5e-195">Atribua automaticamente um valor de **pedido** aos seus pontos de extremidade com base na ordem em que são invocados.</span><span class="sxs-lookup"><span data-stu-id="01d5e-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="01d5e-196">Roteamento de ponto de extremidade no ASP.NET Core 3,0 e posterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="01d5e-197">Não tem um conceito de rotas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="01d5e-198">Não fornece garantias de ordenação para a execução de extensibilidade, todos os pontos de extremidade são processados de uma só vez.</span><span class="sxs-lookup"><span data-stu-id="01d5e-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="01d5e-199">Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="01d5e-200">O [Roteamento de atributos](#ar) é explicado mais adiante neste documento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="01d5e-201">Várias rotas convencionais</span><span class="sxs-lookup"><span data-stu-id="01d5e-201">Multiple conventional routes</span></span>

<span data-ttu-id="01d5e-202">Várias [rotas convencionais](#cr) podem ser adicionadas no `UseEndpoints` adicionando mais chamadas para <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="01d5e-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="01d5e-203">Isso permite a definição de várias convenções ou a adição de rotas convencionais que são dedicadas a uma [ação](#action)específica, como:</span><span class="sxs-lookup"><span data-stu-id="01d5e-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="01d5e-204">A `blog` rota no código anterior é uma **rota convencional dedicada**.</span><span class="sxs-lookup"><span data-stu-id="01d5e-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="01d5e-205">Ele é chamado de rota convencional dedicada porque:</span><span class="sxs-lookup"><span data-stu-id="01d5e-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="01d5e-206">Ele usa o [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="01d5e-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="01d5e-207">Ele é dedicado a uma [ação](#action)específica.</span><span class="sxs-lookup"><span data-stu-id="01d5e-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="01d5e-208">Porque `controller` e `action` não aparecem no modelo de rota `"blog/{*article}"` como parâmetros:</span><span class="sxs-lookup"><span data-stu-id="01d5e-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="01d5e-209">Eles só podem ter os valores padrão `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="01d5e-210">Essa rota sempre é mapeada para a ação `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="01d5e-211">`/Blog`, `/Blog/Article` e `/Blog/{any-string}` são os únicos caminhos de URL que correspondem à rota do blog.</span><span class="sxs-lookup"><span data-stu-id="01d5e-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="01d5e-212">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-212">The preceding example:</span></span>

* <span data-ttu-id="01d5e-213">`blog`a rota tem uma prioridade mais alta para correspondências que a `default` rota porque ela é adicionada primeiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="01d5e-214">É um exemplo de roteamento de estilo de separador, onde é comum ter um [nome de artigo](https://developer.mozilla.org/docs/Glossary/Slug) como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="01d5e-215">No ASP.NET Core 3,0 e posterior, o roteamento não:</span><span class="sxs-lookup"><span data-stu-id="01d5e-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="01d5e-216">Defina um conceito chamado *rota*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-216">Define a concept called a *route*.</span></span> <span data-ttu-id="01d5e-217">`UseRouting`Adiciona correspondência de rota ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="01d5e-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="01d5e-218">O `UseRouting` middleware examina o conjunto de pontos de extremidade definidos no aplicativo e seleciona a melhor correspondência de ponto de extremidades com base na solicitação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="01d5e-219">Forneça garantias sobre a ordem de execução de extensibilidade como <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> ou <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="01d5e-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="01d5e-220">Consulte [Roteamento](xref:fundamentals/routing) para obter material de referência sobre roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="01d5e-221">Ordem de roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="01d5e-221">Conventional routing order</span></span>

<span data-ttu-id="01d5e-222">O roteamento convencional só corresponde a uma combinação de ação e controlador que são definidos pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="01d5e-223">Isso se destina a simplificar os casos em que as rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="01d5e-224">Adicionar rotas usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> atribuir automaticamente um valor de pedido a seus pontos de extremidade com base na ordem em que são invocados.</span><span class="sxs-lookup"><span data-stu-id="01d5e-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="01d5e-225">As correspondências de uma rota que aparece anteriormente têm uma prioridade mais alta.</span><span class="sxs-lookup"><span data-stu-id="01d5e-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="01d5e-226">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="01d5e-227">Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="01d5e-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="01d5e-228">[Rotas convencionais dedicadas](#dcr) com parâmetros de rota catch-all como `{*article}` podem tornar [greedy](xref:fundamentals/routing#greedy)uma rota muito grande, o que significa que ela corresponde às URLs que você pretende corresponder a outras rotas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="01d5e-229">Coloque as rotas de ávido mais tarde na tabela de rotas para evitar correspondências de caminhada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="01d5e-230">Resolvendo ações ambíguas</span><span class="sxs-lookup"><span data-stu-id="01d5e-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="01d5e-231">Quando dois pontos de extremidade correspondem ao roteamento, o roteamento deve seguir um destes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="01d5e-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="01d5e-232">Escolha o melhor candidato.</span><span class="sxs-lookup"><span data-stu-id="01d5e-232">Choose the best candidate.</span></span>
* <span data-ttu-id="01d5e-233">Gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="01d5e-233">Throw an exception.</span></span>

<span data-ttu-id="01d5e-234">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="01d5e-235">O controlador anterior define duas ações que correspondem a:</span><span class="sxs-lookup"><span data-stu-id="01d5e-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="01d5e-236">O caminho da URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="01d5e-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="01d5e-237">Rotear dados `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="01d5e-238">Esse é um padrão típico para controladores MVC:</span><span class="sxs-lookup"><span data-stu-id="01d5e-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="01d5e-239">`Edit(int)`exibe um formulário para editar um produto.</span><span class="sxs-lookup"><span data-stu-id="01d5e-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="01d5e-240">`Edit(int, Product)`processa o formulário Postado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="01d5e-241">Para resolver a rota correta:</span><span class="sxs-lookup"><span data-stu-id="01d5e-241">To resolve the correct route:</span></span>

* <span data-ttu-id="01d5e-242">`Edit(int, Product)`é selecionado quando a solicitação é um HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="01d5e-243">`Edit(int)`é selecionado quando o [verbo http](#verb) é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="01d5e-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="01d5e-244">`Edit(int)`é geralmente chamado por meio de `GET` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="01d5e-245">O <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , é fornecido ao roteamento para que ele possa escolher com base no método http da solicitação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="01d5e-246">O `HttpPostAttribute` faz `Edit(int, Product)` uma correspondência melhor do que `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="01d5e-247">É importante entender a função de atributos como `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="01d5e-248">Atributos semelhantes são definidos para outros [verbos HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="01d5e-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="01d5e-249">No [Roteamento convencional](#cr), é comum que as ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de Mostrar formulário, enviar formulário.</span><span class="sxs-lookup"><span data-stu-id="01d5e-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="01d5e-250">Por exemplo, consulte [examinar os dois métodos de ação de edição](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="01d5e-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="01d5e-251">Se o roteamento não puder escolher um melhor candidato, um <xref:System.Reflection.AmbiguousMatchException> será gerado, listando os vários pontos de extremidade correspondentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="01d5e-252">Nomes de rota convencionais</span><span class="sxs-lookup"><span data-stu-id="01d5e-252">Conventional route names</span></span>

<span data-ttu-id="01d5e-253">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rota convencionais:</span><span class="sxs-lookup"><span data-stu-id="01d5e-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="01d5e-254">Os nomes de rota fornecem um nome lógico à rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-254">The route names give the route a logical name.</span></span> <span data-ttu-id="01d5e-255">A rota nomeada pode ser usada para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="01d5e-256">O uso de uma rota nomeada simplifica a criação de URL quando a ordenação de rotas pode tornar a geração de URL complicada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="01d5e-257">Os nomes de rota devem ser de todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="01d5e-258">Nomes de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-258">Route names:</span></span>

* <span data-ttu-id="01d5e-259">Não têm impacto sobre a correspondência de URL ou o tratamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="01d5e-260">São usados somente para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-260">Are used only for URL generation.</span></span>

<span data-ttu-id="01d5e-261">O conceito de nome de rota é representado no roteamento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="01d5e-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="01d5e-262">Os termos **nome da rota** e **nome do ponto de extremidade**:</span><span class="sxs-lookup"><span data-stu-id="01d5e-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="01d5e-263">São intercambiáveis.</span><span class="sxs-lookup"><span data-stu-id="01d5e-263">Are interchangeable.</span></span>
* <span data-ttu-id="01d5e-264">Qual delas é usada na documentação e no código depende da API que está sendo descrita.</span><span class="sxs-lookup"><span data-stu-id="01d5e-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="01d5e-265">Roteamento de atributos para APIs REST</span><span class="sxs-lookup"><span data-stu-id="01d5e-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="01d5e-266">As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por [verbos HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="01d5e-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="01d5e-267">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="01d5e-268">O código a seguir `StartUp.Configure` é típico para uma API REST e é usado no próximo exemplo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="01d5e-269">No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> é chamado dentro `UseEndpoints` para mapear controladores roteados de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="01d5e-270">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-270">In the following example:</span></span>

* <span data-ttu-id="01d5e-271">O `Configure` método anterior é usado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="01d5e-272">`HomeController`corresponde a um conjunto de URLs semelhantes ao que a rota convencional padrão `{controller=Home}/{action=Index}/{id?}` corresponde.</span><span class="sxs-lookup"><span data-stu-id="01d5e-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="01d5e-273">A `HomeController.Index` ação é executada para qualquer um dos caminhos de URL `/` , `/Home` , `/Home/Index` ou `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="01d5e-274">Este exemplo realça uma importante diferença de programação entre roteamento de atributo e [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="01d5e-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="01d5e-275">O roteamento de atributos requer mais entrada para especificar uma rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="01d5e-276">A rota padrão convencional lida com as rotas mais sucintamente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="01d5e-277">No entanto, o roteamento de atributos permite e exige um controle preciso de quais modelos de rota se aplicam a cada [ação](#action).</span><span class="sxs-lookup"><span data-stu-id="01d5e-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="01d5e-278">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="01d5e-278">In the following code:</span></span>

* <span data-ttu-id="01d5e-279">O nome do controlador e os nomes de ação não desempenham **nenhuma** função na qual a ação seja correspondida.</span><span class="sxs-lookup"><span data-stu-id="01d5e-279">The controller name and action names play **no** role in which action is matched.</span></span>
* <span data-ttu-id="01d5e-280">Corresponde às mesmas URLs do exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-280">Matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="01d5e-281">O código a seguir usa substituição de token para `action` e `controller` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-281">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="01d5e-282">O código a seguir se aplica `[Route("[controller]/[action]")]` ao controlador:</span><span class="sxs-lookup"><span data-stu-id="01d5e-282">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="01d5e-283">No código anterior, os `Index` modelos de método devem preceder `/` ou `~/` até os modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-283">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="01d5e-284">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-284">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="01d5e-285">Consulte [precedência do modelo de rota](xref:fundamentals/routing#rtp) para obter informações sobre a seleção do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-285">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="01d5e-286">Nomes reservados de roteamento</span><span class="sxs-lookup"><span data-stu-id="01d5e-286">Reserved routing names</span></span>

<span data-ttu-id="01d5e-287">As seguintes palavras-chave são nomes de parâmetro de rota reservados ao usar controladores ou Razor páginas:</span><span class="sxs-lookup"><span data-stu-id="01d5e-287">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="01d5e-288">O uso `page` de como um parâmetro de rota com roteamento de atributo é um erro comum.</span><span class="sxs-lookup"><span data-stu-id="01d5e-288">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="01d5e-289">Fazer isso resulta em comportamento inconsistente e confuso com a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-289">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="01d5e-290">Os nomes de parâmetro especiais são usados pela geração de URL para determinar se uma operação de geração de URL se refere a uma Razor página ou a um controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-290">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="01d5e-291">Modelos de verbo HTTP</span><span class="sxs-lookup"><span data-stu-id="01d5e-291">HTTP verb templates</span></span>

<span data-ttu-id="01d5e-292">ASP.NET Core tem os seguintes modelos de verbo HTTP:</span><span class="sxs-lookup"><span data-stu-id="01d5e-292">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="01d5e-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="01d5e-294">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-294">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="01d5e-295">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="01d5e-296">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="01d5e-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="01d5e-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="01d5e-299">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="01d5e-299">Route templates</span></span>

<span data-ttu-id="01d5e-300">ASP.NET Core tem os seguintes modelos de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-300">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="01d5e-301">Todos os [modelos de verbo http](#verb) são modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-301">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="01d5e-302">[Rota](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="01d5e-302">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="01d5e-303">Roteamento de atributos com atributos de verbo http</span><span class="sxs-lookup"><span data-stu-id="01d5e-303">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="01d5e-304">Considere o seguinte controlador:</span><span class="sxs-lookup"><span data-stu-id="01d5e-304">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="01d5e-305">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-305">In the preceding code:</span></span>

* <span data-ttu-id="01d5e-306">Cada ação contém o `[HttpGet]` atributo, que restringe a correspondência somente a solicitações HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="01d5e-306">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="01d5e-307">A `GetProduct` ação inclui o `"{id}"` modelo, portanto, `id` é anexada ao `"api/[controller]"` modelo no controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-307">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="01d5e-308">O modelo de métodos é `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-308">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="01d5e-309">Portanto, essa ação só corresponde a solicitações GET de para o formulário `/api/test2/xyz` , `/api/test2/123` ,, `/api/test2/{any string}` etc.</span><span class="sxs-lookup"><span data-stu-id="01d5e-309">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="01d5e-310">A `GetIntProduct` ação contém o `"int/{id:int}")` modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-310">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="01d5e-311">A `:int` parte do modelo restringe os `id` valores de rota para cadeias de caracteres que podem ser convertidas em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-311">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="01d5e-312">Uma solicitação GET para `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-312">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="01d5e-313">Não corresponde a esta ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-313">Doesn't match this action.</span></span>
  * <span data-ttu-id="01d5e-314">Retorna um erro [404 não encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-314">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="01d5e-315">A `GetInt2Product` ação contém `{id}` no modelo, mas não restringe `id` a valores que podem ser convertidos em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-315">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="01d5e-316">Uma solicitação GET para `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-316">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="01d5e-317">Corresponde a essa rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-317">Matches this route.</span></span>
  * <span data-ttu-id="01d5e-318">A associação de modelo falha ao converter `abc` em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-318">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="01d5e-319">O `id` parâmetro do método é inteiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-319">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="01d5e-320">Retorna uma [solicitação inadequada 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque a associação de modelo não pôde ser convertida `abc` em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-320">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="01d5e-321">O roteamento de atributos pode usar <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="01d5e-321">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="01d5e-322">Todos os atributos de [verbo http](#verb) aceitam um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-322">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="01d5e-323">O exemplo a seguir mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-323">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="01d5e-324">Usando o caminho da URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-324">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="01d5e-325">A `MyProductsController.ListProducts` ação é executada quando o [verbo http](#verb) é `GET` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-325">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="01d5e-326">A `MyProductsController.CreateProduct` ação é executada quando o [verbo http](#verb) é `POST` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-326">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="01d5e-327">Ao criar uma API REST, é raro que você precise usar `[Route(...)]` em um método de ação, pois a ação aceita todos os métodos http.</span><span class="sxs-lookup"><span data-stu-id="01d5e-327">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="01d5e-328">É melhor usar o [atributo de verbo http](#verb) mais específico para ser preciso em relação ao que sua API dá suporte.</span><span class="sxs-lookup"><span data-stu-id="01d5e-328">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="01d5e-329">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-329">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="01d5e-330">As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="01d5e-330">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="01d5e-331">Isso significa que muitas operações, por exemplo, GET e POST no mesmo recurso lógico usam a mesma URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-331">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="01d5e-332">O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.</span><span class="sxs-lookup"><span data-stu-id="01d5e-332">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="01d5e-333">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-333">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="01d5e-334">No exemplo a seguir, `id` é necessário como parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="01d5e-334">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="01d5e-335">A `Products2ApiController.GetProduct(int)` ação:</span><span class="sxs-lookup"><span data-stu-id="01d5e-335">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="01d5e-336">É executado com caminho de URL como`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="01d5e-336">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="01d5e-337">Não é executado com o caminho da URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-337">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="01d5e-338">O atributo [[consume]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que uma ação limite os tipos de conteúdo de solicitação com suporte.</span><span class="sxs-lookup"><span data-stu-id="01d5e-338">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="01d5e-339">Para obter mais informações, consulte [definir tipos de conteúdo de solicitação com suporte com o atributo consumes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="01d5e-339">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="01d5e-340">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-340">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="01d5e-341">Para obter mais informações sobre o `[ApiController]` , consulte o [atributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="01d5e-341">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="01d5e-342">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="01d5e-342">Route name</span></span>

<span data-ttu-id="01d5e-343">O código a seguir define um nome da rota como `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-343">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="01d5e-344">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="01d5e-344">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="01d5e-345">Nomes de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-345">Route names:</span></span>

* <span data-ttu-id="01d5e-346">Não têm impacto sobre o comportamento de correspondência de URL do roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-346">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="01d5e-347">São usados somente para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-347">Are only used for URL generation.</span></span>

<span data-ttu-id="01d5e-348">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-348">Route names must be unique application-wide.</span></span>

<span data-ttu-id="01d5e-349">Compare o código anterior com a rota padrão convencional, que define o `id` parâmetro como opcional ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="01d5e-349">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="01d5e-350">A capacidade de especificar precisamente as APIs tem vantagens, como permitir `/products` e `/products/5` ser expedida para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-350">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="01d5e-351">Combinando rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-351">Combining attribute routes</span></span>

<span data-ttu-id="01d5e-352">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="01d5e-352">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="01d5e-353">Modelos de rota definidos no controlador precedem modelos de rota nas ações. </span><span class="sxs-lookup"><span data-stu-id="01d5e-353">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="01d5e-354">Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-354">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="01d5e-355">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-355">In the preceding example:</span></span>

* <span data-ttu-id="01d5e-356">O caminho da URL `/products` pode corresponder`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="01d5e-356">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="01d5e-357">O caminho da URL `/products/5` pode corresponder `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-357">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="01d5e-358">Ambas as ações correspondem apenas a HTTP `GET` porque elas são marcadas com o `[HttpGet]` atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-358">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="01d5e-359">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-359">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="01d5e-360">O exemplo a seguir corresponde a um conjunto de caminhos de URL semelhante à rota padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-360">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="01d5e-361">A tabela a seguir explica os `[Route]` atributos no código anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-361">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="01d5e-362">Atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-362">Attribute</span></span>               | <span data-ttu-id="01d5e-363">Combina com`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="01d5e-363">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="01d5e-364">Define o modelo de rota</span><span class="sxs-lookup"><span data-stu-id="01d5e-364">Defines route template</span></span> |
| ---
<span data-ttu-id="01d5e-365">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-365">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-366">'Blazor'</span></span>
- <span data-ttu-id="01d5e-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-367">'Identity'</span></span>
- <span data-ttu-id="01d5e-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-369">'Razor'</span></span>
- <span data-ttu-id="01d5e-370">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-371">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-371">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-372">'Blazor'</span></span>
- <span data-ttu-id="01d5e-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-373">'Identity'</span></span>
- <span data-ttu-id="01d5e-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-375">'Razor'</span></span>
- <span data-ttu-id="01d5e-376">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-377">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-377">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-378">'Blazor'</span></span>
- <span data-ttu-id="01d5e-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-379">'Identity'</span></span>
- <span data-ttu-id="01d5e-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-381">'Razor'</span></span>
- <span data-ttu-id="01d5e-382">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-383">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-383">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-384">'Blazor'</span></span>
- <span data-ttu-id="01d5e-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-385">'Identity'</span></span>
- <span data-ttu-id="01d5e-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-387">'Razor'</span></span>
- <span data-ttu-id="01d5e-388">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-389">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-389">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-390">'Blazor'</span></span>
- <span data-ttu-id="01d5e-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-391">'Identity'</span></span>
- <span data-ttu-id="01d5e-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-393">'Razor'</span></span>
- <span data-ttu-id="01d5e-394">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-395">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-395">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-396">'Blazor'</span></span>
- <span data-ttu-id="01d5e-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-397">'Identity'</span></span>
- <span data-ttu-id="01d5e-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-399">'Razor'</span></span>
- <span data-ttu-id="01d5e-400">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-400">'SignalR' uid:</span></span> 

<span data-ttu-id="01d5e-401">--------- | título do---: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-401">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-402">'Blazor'</span></span>
- <span data-ttu-id="01d5e-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-403">'Identity'</span></span>
- <span data-ttu-id="01d5e-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-405">'Razor'</span></span>
- <span data-ttu-id="01d5e-406">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-407">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-407">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-408">'Blazor'</span></span>
- <span data-ttu-id="01d5e-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-409">'Identity'</span></span>
- <span data-ttu-id="01d5e-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-411">'Razor'</span></span>
- <span data-ttu-id="01d5e-412">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-413">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-413">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-414">'Blazor'</span></span>
- <span data-ttu-id="01d5e-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-415">'Identity'</span></span>
- <span data-ttu-id="01d5e-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-417">'Razor'</span></span>
- <span data-ttu-id="01d5e-418">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-419">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-419">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-420">'Blazor'</span></span>
- <span data-ttu-id="01d5e-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-421">'Identity'</span></span>
- <span data-ttu-id="01d5e-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-423">'Razor'</span></span>
- <span data-ttu-id="01d5e-424">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-424">'SignalR' uid:</span></span> 

<span data-ttu-id="01d5e-425">------ | título do---: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-425">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-426">'Blazor'</span></span>
- <span data-ttu-id="01d5e-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-427">'Identity'</span></span>
- <span data-ttu-id="01d5e-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-429">'Razor'</span></span>
- <span data-ttu-id="01d5e-430">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="01d5e-431">Título: autor: Descrição: MS. autor: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="01d5e-431">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="01d5e-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-432">'Blazor'</span></span>
- <span data-ttu-id="01d5e-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="01d5e-433">'Identity'</span></span>
- <span data-ttu-id="01d5e-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="01d5e-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="01d5e-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="01d5e-435">'Razor'</span></span>
- <span data-ttu-id="01d5e-436">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="01d5e-436">'SignalR' uid:</span></span> 

<span data-ttu-id="01d5e-437">----- | | `[Route("")]` | Sim | `"Home"` |
| `[Route("Index")]` | Sim | `"Home/Index"` |
| `[Route("/")]` | **Não** | `""` |
 | `[Route("About")]` | Sim | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="01d5e-437">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="01d5e-438">Ordem de rota de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-438">Attribute route order</span></span>

<span data-ttu-id="01d5e-439">O roteamento cria uma árvore e corresponde a todos os pontos de extremidade simultaneamente:</span><span class="sxs-lookup"><span data-stu-id="01d5e-439">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="01d5e-440">As entradas de rota se comportam como se colocadas em uma ordenação ideal.</span><span class="sxs-lookup"><span data-stu-id="01d5e-440">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="01d5e-441">As rotas mais específicas têm a chance de serem executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="01d5e-441">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="01d5e-442">Por exemplo, uma rota de atributo como `blog/search/{topic}` é mais específica do que uma rota de atributo como `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-442">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="01d5e-443">A `blog/search/{topic}` rota tem prioridade mais alta, por padrão, porque é mais específica.</span><span class="sxs-lookup"><span data-stu-id="01d5e-443">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="01d5e-444">Usando o [Roteamento convencional](#cr), o desenvolvedor é responsável por colocar rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-444">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="01d5e-445">As rotas de atributo podem configurar um pedido usando a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> propriedade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-445">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="01d5e-446">Todos os [atributos de rota](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fornecidos pelo Framework incluem `Order` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-446">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="01d5e-447">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-447">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="01d5e-448">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-448">The default order is `0`.</span></span> <span data-ttu-id="01d5e-449">Definir uma rota usando `Order = -1` execuções antes das rotas que não definem um pedido.</span><span class="sxs-lookup"><span data-stu-id="01d5e-449">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="01d5e-450">Definir uma rota usando `Order = 1` execuções após a ordenação de rota padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-450">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="01d5e-451">**Evite** , dependendo de `Order` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-451">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="01d5e-452">Se o espaço de URL de um aplicativo exigir valores de ordem explícitos para rotear corretamente, isso provavelmente será confuso para os clientes também.</span><span class="sxs-lookup"><span data-stu-id="01d5e-452">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="01d5e-453">Em geral, o roteamento de atributos seleciona a rota correta com correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-453">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="01d5e-454">Se a ordem padrão usada para a geração de URL não estiver funcionando, o uso de um nome de rota como uma substituição normalmente será mais simples do que aplicar a `Order` propriedade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-454">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="01d5e-455">Considere os dois controladores a seguir que definem a rota correspondente `/home` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-455">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="01d5e-456">A solicitação `/home` com o código anterior gera uma exceção semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="01d5e-456">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="01d5e-457">Adicionar `Order` a um dos atributos de rota resolve a ambiguidade:</span><span class="sxs-lookup"><span data-stu-id="01d5e-457">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="01d5e-458">Com o código anterior, `/home` executa o `HomeController.Index` ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-458">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="01d5e-459">Para obter a `MyDemoController.MyIndex` solicitação, `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-459">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="01d5e-460">**Observação**:</span><span class="sxs-lookup"><span data-stu-id="01d5e-460">**Note**:</span></span>

* <span data-ttu-id="01d5e-461">O código anterior é um exemplo de design de roteamento insatisfatório.</span><span class="sxs-lookup"><span data-stu-id="01d5e-461">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="01d5e-462">Ele foi usado para ilustrar a `Order` propriedade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-462">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="01d5e-463">A `Order` Propriedade resolve apenas a ambiguidade, esse modelo não pode ser correspondido.</span><span class="sxs-lookup"><span data-stu-id="01d5e-463">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="01d5e-464">Seria melhor remover o `[Route("Home")]` modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-464">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="01d5e-465">Consulte as [ Razor páginas rota e convenções de aplicativo: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order) para obter informações sobre a ordem de rota com Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-465">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="01d5e-466">Em alguns casos, um erro HTTP 500 é retornado com rotas ambíguas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-466">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="01d5e-467">Use o [registro em log](xref:fundamentals/logging/index) para ver quais pontos de extremidade causaram o `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-467">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="01d5e-468">Substituição de token em modelos de rota [controlador], [ação], [área]</span><span class="sxs-lookup"><span data-stu-id="01d5e-468">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="01d5e-469">Para sua conveniência, as rotas de atributo dão suporte à substituição de token para parâmetros de rota reservados ao colocar um token em um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="01d5e-469">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="01d5e-470">Colchetes:`[]`</span><span class="sxs-lookup"><span data-stu-id="01d5e-470">Square brackets: `[]`</span></span>
* <span data-ttu-id="01d5e-471">Chaves:`{}`</span><span class="sxs-lookup"><span data-stu-id="01d5e-471">Curly braces: `{}`</span></span>

<span data-ttu-id="01d5e-472">Os tokens `[action]` , `[area]` , e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida:</span><span class="sxs-lookup"><span data-stu-id="01d5e-472">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="01d5e-473">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-473">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="01d5e-474">Acordo`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="01d5e-474">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="01d5e-475">Acordo`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="01d5e-475">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="01d5e-476">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-476">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="01d5e-477">O exemplo anterior se comporta da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-477">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="01d5e-478">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="01d5e-478">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="01d5e-479">Isso é poderoso combinado com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="01d5e-479">This is powerful combined with token replacement.</span></span> <span data-ttu-id="01d5e-480">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-480">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="01d5e-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`gera um nome de rota exclusivo para cada ação:</span><span class="sxs-lookup"><span data-stu-id="01d5e-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="01d5e-482">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-482">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="01d5e-483"> gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-483">generates a unique route name for each action.</span></span>

<span data-ttu-id="01d5e-484">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="01d5e-484">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="01d5e-485">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="01d5e-485">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="01d5e-486">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-486">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="01d5e-487">Um transformador de parâmetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="01d5e-487">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="01d5e-488">Por exemplo, um `SlugifyParameterTransformer` transformador de parâmetro personalizado altera o `SubscriptionManagement` valor de rota para `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-488">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="01d5e-489">O <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="01d5e-489">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="01d5e-490">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-490">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="01d5e-491">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-491">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="01d5e-492">O `ListAll` método anterior corresponde a `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-492">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="01d5e-493">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-493">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="01d5e-494">Consulte [MDN Web docs no](https://developer.mozilla.org/docs/Glossary/Slug) separador para obter a definição do separador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-494">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="01d5e-495">Várias rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="01d5e-495">Multiple attribute routes</span></span>

<span data-ttu-id="01d5e-496">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-496">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="01d5e-497">O uso mais comum desse recurso é para simular o comportamento da rota convencional padrão, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-497">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="01d5e-498">Colocar vários atributos de rota no controlador significa que cada um combina com cada um dos atributos de rota nos métodos de ação:</span><span class="sxs-lookup"><span data-stu-id="01d5e-498">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="01d5e-499">Todas as restrições de rota de [verbo http](#verb) implementam `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-499">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="01d5e-500">Quando vários atributos de rota implementados <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> são colocados em uma ação:</span><span class="sxs-lookup"><span data-stu-id="01d5e-500">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="01d5e-501">Cada restrição de ação combina com o modelo de rota aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-501">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="01d5e-502">O uso de várias rotas em ações pode parecer útil e eficiente, é melhor manter o espaço de URL básico e bem definido do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-502">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="01d5e-503">Use várias rotas em ações **somente** quando necessário, por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-503">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="01d5e-504">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-504">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="01d5e-505">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-505">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="01d5e-506">No código anterior, o `[HttpPost("product/{id:int}")]` aplica uma restrição de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-506">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="01d5e-507">A `ProductsController.ShowProduct` ação é correspondida somente por caminhos de URL como `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-507">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="01d5e-508">A parte do modelo de rota `{id:int}` restringe esse segmento a apenas inteiros.</span><span class="sxs-lookup"><span data-stu-id="01d5e-508">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="01d5e-509">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-509">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="01d5e-510">Atributos de rota personalizados usando IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="01d5e-510">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="01d5e-511">Todos os [atributos de rota](#rt) implementam <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="01d5e-511">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="01d5e-512">O tempo de execução de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="01d5e-512">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="01d5e-513">Procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-513">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="01d5e-514">Usa os atributos que implementam `IRouteTemplateProvider` para compilar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-514">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="01d5e-515">Implemente `IRouteTemplateProvider` para definir atributos de rota personalizados.</span><span class="sxs-lookup"><span data-stu-id="01d5e-515">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="01d5e-516">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="01d5e-516">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="01d5e-517">O `Get` método anterior retorna `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-517">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="01d5e-518">Usar o modelo de aplicativo para personalizar as rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-518">Use application model to customize attribute routes</span></span>

<span data-ttu-id="01d5e-519">O modelo de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-519">The application model:</span></span>

* <span data-ttu-id="01d5e-520">É um modelo de objeto criado na inicialização.</span><span class="sxs-lookup"><span data-stu-id="01d5e-520">Is an object model created at startup.</span></span>
* <span data-ttu-id="01d5e-521">Contém todos os metadados usados pelo ASP.NET Core para rotear e executar as ações em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-521">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="01d5e-522">O modelo de aplicativo inclui todos os dados coletados dos atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-522">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="01d5e-523">Os dados dos atributos de rota são fornecidos pela `IRouteTemplateProvider` implementação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-523">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="01d5e-524">Regras</span><span class="sxs-lookup"><span data-stu-id="01d5e-524">Conventions:</span></span>

* <span data-ttu-id="01d5e-525">Pode ser escrito para modificar o modelo de aplicativo para personalizar a forma como o roteamento se comporta.</span><span class="sxs-lookup"><span data-stu-id="01d5e-525">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="01d5e-526">São lidos na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-526">Are read at app startup.</span></span>

<span data-ttu-id="01d5e-527">Esta seção mostra um exemplo básico de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-527">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="01d5e-528">O código a seguir torna as rotas aproximadamente alinhadas com a estrutura de pastas do projeto.</span><span class="sxs-lookup"><span data-stu-id="01d5e-528">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="01d5e-529">O código a seguir impede que a `namespace` Convenção seja aplicada a controladores que são roteados por atributo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-529">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="01d5e-530">Por exemplo, o controlador a seguir não usa `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-530">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="01d5e-531">O método `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-531">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="01d5e-532">Não fará nada se o controlador for um atributo roteado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-532">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="01d5e-533">Define o modelo de controladores com base no `namespace` , com a base `namespace` removida.</span><span class="sxs-lookup"><span data-stu-id="01d5e-533">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="01d5e-534">O `NamespaceRoutingConvention` pode ser aplicado em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-534">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="01d5e-535">Por exemplo, considere o seguinte controlador:</span><span class="sxs-lookup"><span data-stu-id="01d5e-535">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="01d5e-536">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="01d5e-536">In the preceding code:</span></span>

* <span data-ttu-id="01d5e-537">A base `namespace` é `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-537">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="01d5e-538">O nome completo do controlador anterior é `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-538">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="01d5e-539">O `NamespaceRoutingConvention` define o modelo de controladores como `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-539">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="01d5e-540">O `NamespaceRoutingConvention` também pode ser aplicado como um atributo em um controlador:</span><span class="sxs-lookup"><span data-stu-id="01d5e-540">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="01d5e-541">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="01d5e-541">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="01d5e-542">ASP.NET Core aplicativos podem misturar o uso de roteamento convencional e roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-542">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="01d5e-543">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="01d5e-543">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="01d5e-544">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-544">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="01d5e-545">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-545">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="01d5e-546">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="01d5e-546">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="01d5e-547">**Qualquer** atributo de rota no controlador faz com que **todas as** ações no atributo do controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-547">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="01d5e-548">Roteamento de atributos e roteamento convencional usam o mesmo mecanismo de roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-548">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="01d5e-549">Geração de URL e valores de ambiente</span><span class="sxs-lookup"><span data-stu-id="01d5e-549">URL Generation and ambient values</span></span>

<span data-ttu-id="01d5e-550">Os aplicativos podem usar os recursos de geração de URL de roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-550">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="01d5e-551">A geração de URLs elimina URLs de codificação, tornando o código mais robusto e passível de manutenção.</span><span class="sxs-lookup"><span data-stu-id="01d5e-551">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="01d5e-552">Esta seção se concentra nos recursos de geração de URL fornecidos pelo MVC e aborda apenas noções básicas de como funciona a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-552">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="01d5e-553">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-553">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="01d5e-554">A <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface é o elemento subjacente da infraestrutura entre o MVC e o roteamento para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-554">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="01d5e-555">Uma instância do `IUrlHelper` está disponível por meio da `Url` propriedade em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="01d5e-555">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="01d5e-556">No exemplo a seguir, a `IUrlHelper` interface é usada por meio da `Controller.Url` propriedade para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-556">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="01d5e-557">Se o aplicativo estiver usando a rota convencional padrão, o valor da `url` variável será a cadeia de caracteres do caminho da URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-557">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="01d5e-558">Esse caminho de URL é criado pelo roteamento combinando:</span><span class="sxs-lookup"><span data-stu-id="01d5e-558">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="01d5e-559">Os valores de rota da solicitação atual, que são chamados de **valores de ambiente**.</span><span class="sxs-lookup"><span data-stu-id="01d5e-559">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="01d5e-560">Os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-560">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="01d5e-561">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-561">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="01d5e-562">Um parâmetro de rota que não tem um valor pode:</span><span class="sxs-lookup"><span data-stu-id="01d5e-562">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="01d5e-563">Use um valor padrão se ele tiver um.</span><span class="sxs-lookup"><span data-stu-id="01d5e-563">Use a default value if it has one.</span></span>
* <span data-ttu-id="01d5e-564">Será ignorado se for opcional.</span><span class="sxs-lookup"><span data-stu-id="01d5e-564">Be skipped if it's optional.</span></span> <span data-ttu-id="01d5e-565">Por exemplo, o `id` do modelo de rota `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-565">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="01d5e-566">A geração de URL falhará se qualquer parâmetro de rota necessário não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-566">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="01d5e-567">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-567">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="01d5e-568">O exemplo anterior de `Url.Action` assume o [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="01d5e-568">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="01d5e-569">A geração de URL funciona de forma semelhante ao [Roteamento de atributos](#ar), embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-569">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="01d5e-570">Com roteamento convencional:</span><span class="sxs-lookup"><span data-stu-id="01d5e-570">With conventional routing:</span></span>

* <span data-ttu-id="01d5e-571">Os valores de rota são usados para expandir um modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-571">The route values are used to expand a template.</span></span>
* <span data-ttu-id="01d5e-572">Os valores de rota para `controller` e `action` geralmente aparecem nesse modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-572">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="01d5e-573">Isso funciona porque as URLs correspondidas pelo roteamento aderem a uma convenção.</span><span class="sxs-lookup"><span data-stu-id="01d5e-573">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="01d5e-574">O exemplo a seguir usa roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-574">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="01d5e-575">A `Source` ação no código anterior gera `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-575">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="01d5e-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>foi adicionado no ASP.NET Core 3,0 como uma alternativa ao `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="01d5e-577">`LinkGenerator`oferece funcionalidade semelhante, mas mais flexível.</span><span class="sxs-lookup"><span data-stu-id="01d5e-577">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="01d5e-578">Cada método em `IUrlHelper` também tem uma família correspondente de métodos `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-578">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="01d5e-579">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="01d5e-579">Generating URLs by action name</span></span>

<span data-ttu-id="01d5e-580">A [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e todas as sobrecargas relacionadas são projetadas para gerar o ponto de extremidade de destino especificando um nome de controlador e um nome de ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-580">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="01d5e-581">Ao usar `Url.Action` , os valores de rota atuais para `controller` e `action` são fornecidos pelo tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="01d5e-581">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="01d5e-582">O valor de `controller` e `action` são parte de [valores de ambiente](#ambient) e valores.</span><span class="sxs-lookup"><span data-stu-id="01d5e-582">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="01d5e-583">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.</span><span class="sxs-lookup"><span data-stu-id="01d5e-583">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="01d5e-584">O roteamento tenta usar os valores em valores de ambiente para preencher as informações que não foram fornecidas durante a geração de uma URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-584">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="01d5e-585">Considere uma rota como `{a}/{b}/{c}/{d}` com valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-585">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="01d5e-586">O roteamento tem informações suficientes para gerar uma URL sem valores adicionais.</span><span class="sxs-lookup"><span data-stu-id="01d5e-586">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="01d5e-587">O roteamento tem informações suficientes porque todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="01d5e-587">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="01d5e-588">Se o valor `{ d = Donovan }` for adicionado:</span><span class="sxs-lookup"><span data-stu-id="01d5e-588">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="01d5e-589">O valor `{ d = David }` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-589">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="01d5e-590">O caminho de URL gerado é `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-590">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="01d5e-591">**Aviso**: os caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-591">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="01d5e-592">No exemplo anterior, se o valor `{ c = Cheryl }` for adicionado:</span><span class="sxs-lookup"><span data-stu-id="01d5e-592">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="01d5e-593">Ambos os valores `{ c = Carol, d = David }` são ignorados.</span><span class="sxs-lookup"><span data-stu-id="01d5e-593">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="01d5e-594">Não há mais um valor para `d` e a geração de URL falha.</span><span class="sxs-lookup"><span data-stu-id="01d5e-594">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="01d5e-595">Os valores desejados de `c` e `d` devem ser especificados para gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-595">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="01d5e-596">Talvez você espere atingir esse problema com a rota padrão `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-596">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="01d5e-597">Esse problema é raro na prática porque `Url.Action` o sempre especifica explicitamente `controller` um `action` valor e.</span><span class="sxs-lookup"><span data-stu-id="01d5e-597">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="01d5e-598">Várias sobrecargas de [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) usam um objeto de valores de rota para fornecer valores para parâmetros de rota diferentes de `controller` e `action` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-598">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="01d5e-599">O objeto de valores de rota é usado com frequência com `id` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-599">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="01d5e-600">Por exemplo, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-600">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="01d5e-601">O objeto de valores de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-601">The route values object:</span></span>

* <span data-ttu-id="01d5e-602">Por convenção geralmente é um objeto de tipo anônimo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-602">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="01d5e-603">Pode ser um `IDictionary<>` ou um [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="01d5e-603">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="01d5e-604">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="01d5e-604">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="01d5e-605">O código anterior gera `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-605">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="01d5e-606">O código a seguir gera uma URL absoluta:</span><span class="sxs-lookup"><span data-stu-id="01d5e-606">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="01d5e-607">Para criar uma URL absoluta, use um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="01d5e-607">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="01d5e-608">Uma sobrecarga que aceita um `protocol` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-608">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="01d5e-609">Por exemplo, o código anterior.</span><span class="sxs-lookup"><span data-stu-id="01d5e-609">For example, the preceding code.</span></span>
* <span data-ttu-id="01d5e-610">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que gera URIs absolutos por padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-610">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="01d5e-611">Gerar URLs por rota</span><span class="sxs-lookup"><span data-stu-id="01d5e-611">Generate URLs by route</span></span>

<span data-ttu-id="01d5e-612">O código anterior demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-612">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="01d5e-613">`IUrlHelper`também fornece a família de métodos [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="01d5e-613">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="01d5e-614">Esses métodos são semelhantes a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-614">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="01d5e-615">O uso mais comum de `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-615">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="01d5e-616">Especifica um nome de rota para gerar a URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-616">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="01d5e-617">Geralmente, não especifica um controlador ou um nome de ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-617">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="01d5e-618">O arquivo a seguir Razor gera um link HTML para `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-618">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="01d5e-619">Gerar URLs em HTML eRazor</span><span class="sxs-lookup"><span data-stu-id="01d5e-619">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="01d5e-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>fornece os <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> métodos [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para gerar `<form>` e `<a>` elementos, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="01d5e-621">Esses métodos usam o método [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-621">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="01d5e-622">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="01d5e-622">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="01d5e-623">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-623">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="01d5e-624">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-624">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="01d5e-625">Consulte [auxiliares de marca em formulários](xref:mvc/views/working-with-forms) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-625">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="01d5e-626">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="01d5e-626">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="01d5e-627">Geração de URL nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="01d5e-627">URL generation in Action Results</span></span>

<span data-ttu-id="01d5e-628">Os exemplos anteriores mostraram o uso `IUrlHelper` de em um controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-628">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="01d5e-629">O uso mais comum em um controlador é gerar uma URL como parte de um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-629">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="01d5e-630">As classes base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-630">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="01d5e-631">Um uso típico é redirecionar depois de aceitar a entrada do usuário:</span><span class="sxs-lookup"><span data-stu-id="01d5e-631">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="01d5e-632">A ação resulta em métodos de fábrica como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> seguem um padrão semelhante para os métodos em `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-632">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="01d5e-633">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="01d5e-633">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="01d5e-634">O [Roteamento convencional](#cr) pode usar um tipo especial de definição de rota chamada de [rota convencional dedicada](#dcr).</span><span class="sxs-lookup"><span data-stu-id="01d5e-634">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="01d5e-635">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada:</span><span class="sxs-lookup"><span data-stu-id="01d5e-635">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="01d5e-636">Usando as definições de rota anteriores, `Url.Action("Index", "Home")` o gera o caminho da URL `/` usando a `default` rota, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="01d5e-636">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="01d5e-637">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-637">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="01d5e-638">As [rotas convencionais dedicadas](#dcr) contam com um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impede que a rota seja muito [ávido](xref:fundamentals/routing#greedy) com a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-638">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="01d5e-639">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-639">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="01d5e-640">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-640">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="01d5e-641">A geração de URL usando `blog` falha porque os valores `{ controller = Home, action = Index }` não correspondem `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-641">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="01d5e-642">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="01d5e-642">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="01d5e-643">Áreas</span><span class="sxs-lookup"><span data-stu-id="01d5e-643">Areas</span></span>

<span data-ttu-id="01d5e-644">As [áreas](xref:mvc/controllers/areas) são um recurso do MVC usado para organizar a funcionalidade relacionada em um grupo como separado:</span><span class="sxs-lookup"><span data-stu-id="01d5e-644">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="01d5e-645">Namespace de roteamento para ações do controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-645">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="01d5e-646">Estrutura de pastas para exibições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-646">Folder structure for views.</span></span>

<span data-ttu-id="01d5e-647">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que eles tenham áreas diferentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-647">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="01d5e-648">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-648">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="01d5e-649">Esta seção discute como o roteamento interage com áreas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-649">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="01d5e-650">Consulte [áreas](xref:mvc/controllers/areas) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-650">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="01d5e-651">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma `area` rota para um `area` nome `Blog` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-651">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="01d5e-652">No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> é chamado para criar o `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-652">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="01d5e-653">O segundo parâmetro, `"Blog"` , é o nome da área.</span><span class="sxs-lookup"><span data-stu-id="01d5e-653">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="01d5e-654">Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser` , a `"blog_route"` rota gera os valores de rota `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-654">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="01d5e-655">O `area` valor de rota é produzido por um valor padrão para `area` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-655">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="01d5e-656">A rota criada pelo `MapAreaControllerRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="01d5e-656">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="01d5e-657">`MapAreaControllerRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-657">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="01d5e-658">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-658">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="01d5e-659">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-659">Conventional routing is order-dependent.</span></span> <span data-ttu-id="01d5e-660">Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="01d5e-660">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="01d5e-661">Usando o exemplo anterior, os valores de rota `{ area = Blog, controller = Users, action = AddUser }` correspondem à seguinte ação:</span><span class="sxs-lookup"><span data-stu-id="01d5e-661">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="01d5e-662">O atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) é o que denota um controlador como parte de uma área.</span><span class="sxs-lookup"><span data-stu-id="01d5e-662">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="01d5e-663">Esse controlador está na `Blog` área.</span><span class="sxs-lookup"><span data-stu-id="01d5e-663">This controller is in the `Blog` area.</span></span> <span data-ttu-id="01d5e-664">Os controladores sem um `[Area]` atributo não são membros de nenhuma área e **não** correspondem quando o `area` valor de rota é fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-664">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="01d5e-665">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-665">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="01d5e-666">O namespace de cada controlador é mostrado aqui para fins de integridade.</span><span class="sxs-lookup"><span data-stu-id="01d5e-666">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="01d5e-667">Se os controladores anteriores usarem o mesmo namespace, um erro do compilador será gerado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-667">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="01d5e-668">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="01d5e-668">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="01d5e-669">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-669">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="01d5e-670">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-670">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="01d5e-671">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="01d5e-671">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="01d5e-672">Ao executar uma ação dentro de uma área, o valor de rota para `area` está disponível como um [valor de ambiente](#ambient) para roteamento a ser usado para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-672">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="01d5e-673">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="01d5e-673">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="01d5e-674">O código a seguir gera uma URL para `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="01d5e-674">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="01d5e-675">Definição de ação</span><span class="sxs-lookup"><span data-stu-id="01d5e-675">Action definition</span></span>

<span data-ttu-id="01d5e-676">Os métodos públicos em um controlador, exceto aqueles com o atributo [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , são ações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-676">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="01d5e-677">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="01d5e-677">Sample code</span></span>

 * <span data-ttu-id="01d5e-678">O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-678">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="01d5e-679">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01d5e-679">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01d5e-680">O ASP.NET Core MVC usa o [middleware](xref:fundamentals/middleware/index) de Roteamento para fazer as correspondências das URLs de solicitações de entrada e mapeá-las para ações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-680">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="01d5e-681">As rotas são definidas em atributos ou no código de inicialização.</span><span class="sxs-lookup"><span data-stu-id="01d5e-681">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="01d5e-682">Elas descrevem como deve ser feita a correspondência entre caminhos de URL e ações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-682">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="01d5e-683">As rotas também são usadas para gerar URLs (para links) enviados em resposta.</span><span class="sxs-lookup"><span data-stu-id="01d5e-683">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="01d5e-684">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-684">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="01d5e-685">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-685">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="01d5e-686">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="01d5e-686">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="01d5e-687">Este documento explicará as interações entre o MVC e o roteamento e como aplicativos MVC comuns usam recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-687">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="01d5e-688">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes sobre o roteamento avançado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-688">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="01d5e-689">Configurando o middleware de Roteamento</span><span class="sxs-lookup"><span data-stu-id="01d5e-689">Setting up Routing Middleware</span></span>

<span data-ttu-id="01d5e-690">No método *Configurar*, você poderá ver código semelhante a:</span><span class="sxs-lookup"><span data-stu-id="01d5e-690">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="01d5e-691">Dentro da chamada para `UseMvc`, `MapRoute` é usado para criar uma única rota, que chamaremos de rota `default`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-691">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="01d5e-692">A maioria dos aplicativos MVC usa uma rota com um modelo semelhante à rota `default`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-692">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="01d5e-693">O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` pode corresponder a um caminho de URL como `/Products/Details/5` e extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` gerando tokens para o caminho.</span><span class="sxs-lookup"><span data-stu-id="01d5e-693">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="01d5e-694">O MVC tentará localizar um controlador chamado `ProductsController` e executar a ação `Details`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-694">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="01d5e-695">Observe que, neste exemplo, o model binding usaria o valor de `id = 5` para definir o parâmetro `id` como `5` ao invocar essa ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-695">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="01d5e-696">Consulte [Model binding](../models/model-binding.md) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-696">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="01d5e-697">Usando a rota `default`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-697">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="01d5e-698">O modelo da rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-698">The route template:</span></span>

* <span data-ttu-id="01d5e-699">`{controller=Home}` define `Home` como o `controller` padrão</span><span class="sxs-lookup"><span data-stu-id="01d5e-699">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="01d5e-700">`{action=Index}` define `Index` como o `action` padrão</span><span class="sxs-lookup"><span data-stu-id="01d5e-700">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="01d5e-701">`{id?}` define `id` como opcional</span><span class="sxs-lookup"><span data-stu-id="01d5e-701">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="01d5e-702">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="01d5e-702">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="01d5e-703">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-703">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="01d5e-704">`"{controller=Home}/{action=Index}/{id?}"` pode corresponder ao caminho da URL `/` e produzirá os valores de rota `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-704">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="01d5e-705">Os valores de `controller` e `action` usam os valores padrão, `id` não produz um valor, uma vez que não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-705">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="01d5e-706">O MVC usaria esses valores de rota para selecionar a ação `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-706">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="01d5e-707">Usando essa definição de controlador e modelo de rota, a ação `HomeController.Index` seria executada para qualquer um dos caminhos de URL a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-707">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="01d5e-708">O método de conveniência `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-708">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="01d5e-709">Pode ser usado para substituir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-709">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="01d5e-710">`UseMvc` e `UseMvcWithDefaultRoute` adicionam uma instância de `RouterMiddleware` ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="01d5e-710">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="01d5e-711">O MVC não interage diretamente com o middleware e usa o roteamento para tratar das solicitações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-711">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="01d5e-712">O MVC é conectado às rotas por meio de uma instância de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-712">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="01d5e-713">O código dentro de `UseMvc` é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="01d5e-713">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="01d5e-714">`UseMvc` não define diretamente nenhuma rota, ele adiciona um espaço reservado à coleção de rotas para a rota `attribute`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-714">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="01d5e-715">A sobrecarga `UseMvc(Action<IRouteBuilder>)` permite adicionar suas próprias rotas e também dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-715">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="01d5e-716">`UseMvc`e todas as suas variações adicionam um espaço reservado para o atributo roteamento de atributo de rota está sempre disponível, independentemente de como você configura `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-716">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="01d5e-717">`UseMvcWithDefaultRoute` define uma rota padrão e dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-717">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="01d5e-718">A seção [Roteamento de atributos](#attribute-routing-ref-label) inclui mais detalhes sobre o roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-718">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="01d5e-719">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="01d5e-719">Conventional routing</span></span>

<span data-ttu-id="01d5e-720">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-720">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="01d5e-721">O código anterior é um exemplo de roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="01d5e-721">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="01d5e-722">Esse estilo é chamado de roteamento convencional porque ele estabelece uma *Convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="01d5e-722">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="01d5e-723">O primeiro segmento de caminho mapeia para o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-723">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="01d5e-724">O segundo mapeia para o nome da ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-724">The second maps to the action name.</span></span>
* <span data-ttu-id="01d5e-725">O terceiro segmento é usado para um opcional `id` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-725">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="01d5e-726">`id`mapeia para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-726">`id` maps to a model entity.</span></span>

<span data-ttu-id="01d5e-727">Usando essa rota `default`, o caminho da URL `/Products/List` é mapeado para a ação `ProductsController.List` e `/Blog/Article/17` é mapeado para `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-727">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="01d5e-728">Esse mapeamento é baseado **somente** nos nomes do controlador e da ação e não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="01d5e-728">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="01d5e-729">Usar o roteamento convencional com a rota padrão permite compilar o aplicativo rapidamente sem precisar criar um novo padrão de URL para cada ação que você definir.</span><span class="sxs-lookup"><span data-stu-id="01d5e-729">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="01d5e-730">Para um aplicativo com ações de estilo CRUD, ter consistência para as URLs em seus controladores pode ajudar a simplificar seu código e a tornar sua interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="01d5e-730">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="01d5e-731">O `id` é definido como opcional pelo modelo de rota, o que significa que suas ações podem ser executadas sem a ID fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-731">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="01d5e-732">Normalmente, o que acontecerá se `id` for omitido da URL é que ele será definido como `0` pelo model binding e, dessa forma, não será encontrada no banco de dados nenhuma entidade correspondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-732">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="01d5e-733">O roteamento de atributos pode lhe proporcionar controle refinado para tornar a ID obrigatória para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="01d5e-733">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="01d5e-734">Por convenção, a documentação incluirá parâmetros opcionais, como `id`, quando for provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="01d5e-734">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="01d5e-735">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="01d5e-735">Multiple routes</span></span>

<span data-ttu-id="01d5e-736">É possível adicionar várias rotas dentro de `UseMvc` adicionando mais chamadas para `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-736">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="01d5e-737">Fazer isso permite que você defina várias convenções ou que adicione rotas convencionais dedicadas a uma ação específica, como:</span><span class="sxs-lookup"><span data-stu-id="01d5e-737">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="01d5e-738">A rota `blog` aqui é uma *rota convencional dedicada*, o que significa que ela usa o sistema de roteamento convencional, mas é dedicada a uma ação específica.</span><span class="sxs-lookup"><span data-stu-id="01d5e-738">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="01d5e-739">Como `controller` e `action` não aparecem no modelo de rota como parâmetros, eles só podem ter os valores padrão e, portanto, essa rota sempre será mapeada para a ação `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-739">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="01d5e-740">As rotas na coleção de rotas são ordenadas e serão processadas na ordem em que forem adicionadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-740">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="01d5e-741">Portanto, neste exemplo, a rota `blog` será tentada antes da rota `default`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-741">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-742">*Rotas convencionais dedicadas* geralmente usam parâmetros de rota **catch-all** como `{*article}` para capturar a parte restante do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-742">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="01d5e-743">Isso pode fazer com que uma rota fique "muito ambiciosa", ou seja, que faça a correspondência com URLs que deveriam ser correspondidas com outras rotas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-743">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="01d5e-744">Coloque as rotas "ambiciosas" mais adiante na tabela de rotas para solucionar esse problema.</span><span class="sxs-lookup"><span data-stu-id="01d5e-744">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="01d5e-745">Fallback</span><span class="sxs-lookup"><span data-stu-id="01d5e-745">Fallback</span></span>

<span data-ttu-id="01d5e-746">Como parte do processamento de solicitações, o MVC verificará se o valores das rotas podem ser usados para encontrar um controlador e uma ação em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-746">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="01d5e-747">Se os valores das rotas não corresponderem a uma ação, a rota não será considerada correspondente e a próxima rota será tentada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-747">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="01d5e-748">Isso é chamado de *fallback* e sua finalidade é simplificar casos em que rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-748">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="01d5e-749">Desambiguação de ações</span><span class="sxs-lookup"><span data-stu-id="01d5e-749">Disambiguating actions</span></span>

<span data-ttu-id="01d5e-750">Quando duas ações correspondem por meio do roteamento, o MVC precisa resolver a ambiguidade para escolher a "melhor" candidata ou lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="01d5e-750">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="01d5e-751">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-751">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="01d5e-752">Esse controlador define duas ações que fariam a correspondência entre caminho da URL `/Products/Edit/17` e a os dados da rota `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-752">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="01d5e-753">Este é um padrão comum para controladores MVC em que `Edit(int)` mostra um formulário para editar um produto e `Edit(int, Product)` processa o formulário postado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-753">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="01d5e-754">Para que isso seja possível, o MVC precisa escolher `Edit(int, Product)` quando a solicitação é um `POST` HTTP e `Edit(int)` quando o verbo HTTP é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="01d5e-754">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="01d5e-755">O `HttpPostAttribute` (`[HttpPost]`) é uma implementação de `IActionConstraint` que só permite que a ação seja selecionada quando o verbo HTTP é `POST`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-755">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="01d5e-756">A presença de um `IActionConstraint` faz do `Edit(int, Product)` uma "melhor" correspondência do que `Edit(int)`, portanto, `Edit(int, Product)` será tentado primeiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-756">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="01d5e-757">Você só precisará gravar implementações personalizadas de `IActionConstraint` em cenários especializados, mas é importante compreender a função de atributos como `HttpPostAttribute` – atributos semelhantes são definidos para outros verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="01d5e-757">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="01d5e-758">No roteamento convencional, é comum que ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-758">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="01d5e-759">A conveniência desse padrão ficará mais aparente após você revisar a seção [Noções básicas sobre IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="01d5e-759">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="01d5e-760">Se várias rotas corresponderem e o MVC não puder encontrar uma rota "melhor", ele gerará um `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-760">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="01d5e-761">Nomes de rotas</span><span class="sxs-lookup"><span data-stu-id="01d5e-761">Route names</span></span>

<span data-ttu-id="01d5e-762">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas:</span><span class="sxs-lookup"><span data-stu-id="01d5e-762">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="01d5e-763">Os nomes de rotas dão a uma rota um nome lógico, de modo que a rota nomeada possa ser usada para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-763">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="01d5e-764">Isso simplifica muito a criação de URLs quando a ordenação de rotas poderia complicá-la.</span><span class="sxs-lookup"><span data-stu-id="01d5e-764">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="01d5e-765">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-765">Route names must be unique application-wide.</span></span>

<span data-ttu-id="01d5e-766">Os nomes de rotas não têm impacto sobre a correspondência de URLs ou o tratamento de solicitações; eles são usados apenas para a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="01d5e-766">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="01d5e-767">[Roteamento](xref:fundamentals/routing) tem informações mais detalhadas sobre geração de URLs, incluindo a geração de URLs em auxiliares específicos do MVC.</span><span class="sxs-lookup"><span data-stu-id="01d5e-767">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="01d5e-768">Roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-768">Attribute routing</span></span>

<span data-ttu-id="01d5e-769">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-769">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="01d5e-770">No exemplo a seguir, `app.UseMvc();` é usado no método `Configure` e nenhuma rota é passada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-770">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="01d5e-771">O `HomeController` corresponderá a um conjunto de URLs semelhantes ao que a rota padrão `{controller=Home}/{action=Index}/{id?}` corresponderia:</span><span class="sxs-lookup"><span data-stu-id="01d5e-771">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="01d5e-772">A ação `HomeController.Index()` será executada para qualquer um dos caminhos de URL `/`, `/Home` ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-772">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-773">Este exemplo destaca uma diferença importante de programação entre o roteamento de atributo e o roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="01d5e-773">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="01d5e-774">O roteamento de atributo requer mais entradas para especificar uma rota; a rota padrão convencional manipula as rotas de forma mais sucinta.</span><span class="sxs-lookup"><span data-stu-id="01d5e-774">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="01d5e-775">No entanto, o roteamento de atributo permite (e exige) o controle preciso de quais modelos de rota se aplicam a cada ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-775">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="01d5e-776">Com o roteamento de atributo, o nome do controlador e os nomes de ação não desempenham **nenhuma** função quanto a qual ação é selecionada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-776">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="01d5e-777">Este exemplo corresponderá as mesmas URLs que o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="01d5e-777">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="01d5e-778">Os modelos de rota acima não definem parâmetros de rota para `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-778">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="01d5e-779">Na verdade, esses parâmetros de rota não são permitidos em rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-779">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="01d5e-780">Uma vez que o modelo de rota já está associado a uma ação, não faria sentido analisar o nome da ação da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-780">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="01d5e-781">Roteamento de atributo com atributos Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="01d5e-781">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="01d5e-782">O roteamento de atributo também pode usar atributos `Http[Verb]`, como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-782">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="01d5e-783">Todos esses atributos podem aceitar um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-783">All of these attributes can accept a route template.</span></span> <span data-ttu-id="01d5e-784">Este exemplo mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-784">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="01d5e-785">Para um caminho de URL como `/products`, a ação `ProductsApi.ListProducts` será executada quando o verbo HTTP for `GET` e `ProductsApi.CreateProduct` será executado quando o verbo HTTP for `POST`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-785">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="01d5e-786">Primeiro, o roteamento de atributo faz a correspondência da URL com o conjunto de modelos de rota definidos por atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-786">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="01d5e-787">Quando um modelo de rota for correspondente, restrições de `IActionConstraint` serão aplicadas para determinar quais ações podem ser executadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-787">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="01d5e-788">Ao criar uma API REST, é raro que você queira usar `[Route(...)]` em um método de ação, pois a ação aceitará todos os métodos http.</span><span class="sxs-lookup"><span data-stu-id="01d5e-788">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="01d5e-789">É melhor usar o `Http*Verb*Attributes` mais específico para ser preciso quanto ao que tem suporte de sua API.</span><span class="sxs-lookup"><span data-stu-id="01d5e-789">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="01d5e-790">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-790">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="01d5e-791">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-791">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="01d5e-792">Neste exemplo, `id` é obrigatório como parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-792">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="01d5e-793">A ação `ProductsApi.GetProduct(int)` será executada para um caminho de URL como `/products/3`, mas não para um caminho de URL como `/products`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-793">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="01d5e-794">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-794">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="01d5e-795">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="01d5e-795">Route Name</span></span>

<span data-ttu-id="01d5e-796">O código a seguir define um *nome da rota* como `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-796">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="01d5e-797">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="01d5e-797">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="01d5e-798">Nomes de rota não têm impacto sobre o comportamento de correspondência da URL e são usados somente para geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="01d5e-798">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="01d5e-799">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-799">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-800">Compare isso com a *rota padrão* convencional, que define o parâmetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="01d5e-800">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="01d5e-801">Essa capacidade de especificar APIs de forma específica tem vantagens, como permitir que `/products` e `/products/5` sejam expedidos para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-801">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="01d5e-802">Combinando rotas</span><span class="sxs-lookup"><span data-stu-id="01d5e-802">Combining routes</span></span>

<span data-ttu-id="01d5e-803">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="01d5e-803">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="01d5e-804">Modelos de rota definidos no controlador precedem modelos de rota nas ações. </span><span class="sxs-lookup"><span data-stu-id="01d5e-804">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="01d5e-805">Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-805">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="01d5e-806">Neste exemplo, o caminho de URL `/products` pode corresponder a `ProductsApi.ListProducts` e o caminho de URL `/products/5` pode corresponder a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-806">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="01d5e-807">Ambas as ações correspondem apenas a HTTP `GET` porque elas são marcadas com o `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-807">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="01d5e-808">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-808">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="01d5e-809">Este exemplo corresponde a um conjunto de caminhos de URL semelhante à *rota padrão*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-809">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="01d5e-810">Ordenando rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="01d5e-810">Ordering attribute routes</span></span>

<span data-ttu-id="01d5e-811">Ao contrário das rotas convencionais, que são executadas em uma ordem definida, o roteamento de atributo cria uma árvore e corresponde a todas as rotas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-811">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="01d5e-812">O comportamento é como se as entradas de rota fossem colocadas em uma ordem ideal; as rotas mais específicas têm uma chance de ser executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="01d5e-812">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="01d5e-813">Por exemplo, uma rota como `blog/search/{topic}` é mais específica que uma rota como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-813">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="01d5e-814">Em termos da lógica, a rota `blog/search/{topic}` é "executada" primeiro, por padrão, porque essa é a única ordem que faz sentido.</span><span class="sxs-lookup"><span data-stu-id="01d5e-814">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="01d5e-815">Usando o roteamento convencional, o desenvolvedor é responsável por colocar as rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-815">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="01d5e-816">Rotas de atributos podem configurar uma ordem, usando a propriedade `Order` de todos os atributos de rota fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="01d5e-816">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="01d5e-817">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-817">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="01d5e-818">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-818">The default order is `0`.</span></span> <span data-ttu-id="01d5e-819">Uma rota definida usando `Order = -1` será executada antes de rotas que não definem uma ordem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-819">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="01d5e-820">Uma rota definida usando `Order = 1` será executada após a ordem das rotas padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-820">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="01d5e-821">Evite depender de `Order`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-821">Avoid depending on `Order`.</span></span> <span data-ttu-id="01d5e-822">Se o seu espaço de URL exigir valores de ordem explícita para fazer o roteamento corretamente, provavelmente ele também será confuso para os clientes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-822">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="01d5e-823">De modo geral, o roteamento de atributos selecionará a rota correta com a correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-823">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="01d5e-824">Se a ordem padrão usada para a geração de URL não estiver funcionando, usar o nome da rota como uma substituição geralmente será mais simples do que aplicar a propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-824">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="01d5e-825">O roteamento de páginas e o roteamento do controlador MVC compartilham uma implementação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-825"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="01d5e-826">As informações sobre a ordem de rota nos Razor Tópicos de páginas estão disponíveis em [ Razor páginas rota e convenções de aplicativo: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="01d5e-826">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="01d5e-827">Substituição de token em modelos de rota ([controlador] [ação] [área])</span><span class="sxs-lookup"><span data-stu-id="01d5e-827">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="01d5e-828">Para sua conveniência, as rotas de atributo dão suporte à *substituição de token* ao colocar um token entre colchetes ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="01d5e-828">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="01d5e-829">Os tokens `[action]`, `[area]` e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida.</span><span class="sxs-lookup"><span data-stu-id="01d5e-829">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="01d5e-830">No exemplo a seguir, as ações correspondem a caminhos de URL conforme descrito nos comentários:</span><span class="sxs-lookup"><span data-stu-id="01d5e-830">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="01d5e-831">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-831">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="01d5e-832">O exemplo acima se comportará da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-832">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="01d5e-833">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="01d5e-833">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="01d5e-834">Isso é especialmente eficiente em combinação com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="01d5e-834">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="01d5e-835">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-835">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="01d5e-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="01d5e-837">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="01d5e-837">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="01d5e-838">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="01d5e-838">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="01d5e-839">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-839">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="01d5e-840">Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="01d5e-840">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="01d5e-841">Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-841">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="01d5e-842">O `RouteTokenTransformerConvention` é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="01d5e-842">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="01d5e-843">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-843">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="01d5e-844">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-844">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="01d5e-845">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-845">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="01d5e-846">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="01d5e-846">Multiple Routes</span></span>

<span data-ttu-id="01d5e-847">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-847">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="01d5e-848">O uso mais comum desse recurso é para simular o comportamento da *rota convencional padrão*, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-848">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="01d5e-849">Colocar vários atributos de rota no controlador significa que cada um deles será combinado com cada um dos atributos de rota nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-849">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="01d5e-850">Quando vários atributos de rota (que implementam `IActionConstraint`) são colocados em uma ação, cada restrição da ação combina com o modelo de rota do atributo que a definiu.</span><span class="sxs-lookup"><span data-stu-id="01d5e-850">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="01d5e-851">Embora o uso de várias rotas em ações possa parecer eficaz, é melhor manter o espaço de URL de seu aplicativo simples e bem definido.</span><span class="sxs-lookup"><span data-stu-id="01d5e-851">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="01d5e-852">Use várias rotas em ações somente quando for necessário; por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-852">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="01d5e-853">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-853">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="01d5e-854">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-854">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="01d5e-855">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-855">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="01d5e-856">Atributos de rota personalizados usando `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="01d5e-856">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="01d5e-857">Todos os atributos de rota fornecidos na estrutura ( `[Route(...)]`, `[HttpGet(...)]` etc.) implementam a interface `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-857">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="01d5e-858">O MVC procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado e usa aqueles que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-858">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="01d5e-859">Você pode implementar `IRouteTemplateProvider` para definir seus próprios atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-859">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="01d5e-860">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="01d5e-860">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="01d5e-861">O atributo do exemplo acima configura automaticamente o `Template` como `"api/[controller]"` quando `[MyApiController]` é aplicado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-861">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="01d5e-862">Usando o Modelo de Aplicativo para personalizar rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="01d5e-862">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="01d5e-863">O *modelo de aplicativo* é um modelo de objeto criado durante a inicialização com todos os metadados usados pelo MVC para rotear e executar suas ações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-863">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="01d5e-864">O *modelo de aplicativo* inclui todos os dados reunidos dos atributos de rota (por meio de `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="01d5e-864">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="01d5e-865">Você pode escrever *convenções* para modificar o modelo do aplicativo no momento da inicialização para personalizar o comportamento do roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-865">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="01d5e-866">Esta seção mostra um exemplo simples de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-866">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="01d5e-867">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="01d5e-867">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="01d5e-868">Aplicativos MVC podem combinar o uso do roteamento convencional e do roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-868">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="01d5e-869">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="01d5e-869">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="01d5e-870">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-870">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="01d5e-871">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-871">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="01d5e-872">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="01d5e-872">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="01d5e-873">**Qualquer** atributo de rota no controlador faz com que todas as ações no atributo de controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-873">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-874">O que diferencia os dois tipos de sistemas de roteamento é o processo aplicado após uma URL corresponder a um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-874">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="01d5e-875">No roteamento convencional, os valores de rota da correspondência são usados para escolher a ação e o controlador em uma tabela de pesquisa com todas as ações roteadas convencionais.</span><span class="sxs-lookup"><span data-stu-id="01d5e-875">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="01d5e-876">No roteamento de atributo, cada modelo já está associado a uma ação e nenhuma pesquisa adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="01d5e-876">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="01d5e-877">Segmentos complexos</span><span class="sxs-lookup"><span data-stu-id="01d5e-877">Complex segments</span></span>

<span data-ttu-id="01d5e-878">Segmentos complexos (por exemplo, `[Route("/dog{token}cat")]`), são processados combinando literais da direita para a esquerda de uma maneira não Greedy.</span><span class="sxs-lookup"><span data-stu-id="01d5e-878">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="01d5e-879">Veja [o código-fonte](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) para obter uma descrição.</span><span class="sxs-lookup"><span data-stu-id="01d5e-879">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="01d5e-880">Para obter mais informações, confira [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="01d5e-880">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="01d5e-881">Geração de URL</span><span class="sxs-lookup"><span data-stu-id="01d5e-881">URL Generation</span></span>

<span data-ttu-id="01d5e-882">Aplicativos MVC podem usar os recursos de geração de URL do roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-882">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="01d5e-883">Gerar URLs elimina a necessidade de codificar URLs, tornando seu código mais robusto e sustentável.</span><span class="sxs-lookup"><span data-stu-id="01d5e-883">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="01d5e-884">Esta seção tem como foco os recursos de geração de URL fornecidos pelo MVC e só aborda as noções básicas de como a geração de URL funciona.</span><span class="sxs-lookup"><span data-stu-id="01d5e-884">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="01d5e-885">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-885">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="01d5e-886">A interface `IUrlHelper` é a parte subjacente da infraestrutura entre o MVC e o roteamento para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-886">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="01d5e-887">Você encontrará uma instância de `IUrlHelper` disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="01d5e-887">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="01d5e-888">Neste exemplo, a interface `IUrlHelper` é usada por meio a propriedade `Controller.Url` para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-888">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="01d5e-889">Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho de URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-889">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="01d5e-890">Esse caminho de URL é criado pelo roteamento combinando os valores de rota da solicitação atual (valores de ambiente) com os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="01d5e-890">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="01d5e-891">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-891">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="01d5e-892">Um parâmetro de rota que não tem um valor pode usar um valor padrão se houver um ou pode ser ignorado se for opcional (como no caso de `id` neste exemplo).</span><span class="sxs-lookup"><span data-stu-id="01d5e-892">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="01d5e-893">A geração de URL falhará se qualquer parâmetro de rota obrigatório não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-893">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="01d5e-894">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-894">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="01d5e-895">O exemplo de `Url.Action` acima pressupõe que o roteamento seja convencional, mas a geração de URL funciona de forma semelhante com o roteamento de atributo, embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-895">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="01d5e-896">Com o roteamento convencional, os valores de rota são usados para expandir um modelo e os valores de rota para `controller` e `action` normalmente são exibidos no modelo – isso funciona porque as URLs correspondidas pelo roteamento aderem a uma *convenção*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-896">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="01d5e-897">No roteamento de atributo, os valores de rota para `controller` e `action` não podem ser exibidos no modelo; em vez disso, eles são usados para pesquisar o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-897">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="01d5e-898">Este exemplo usa o roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="01d5e-898">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="01d5e-899">O MVC cria uma tabela de pesquisa de todas as ações de atributo roteadas e faz a correspondência dos valores de `controller` e `action` para selecionar o modelo de rota a ser usado para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-899">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="01d5e-900">Na amostra acima, `custom/url/to/destination` é gerado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-900">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="01d5e-901">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="01d5e-901">Generating URLs by action name</span></span>

<span data-ttu-id="01d5e-902">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="01d5e-902">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="01d5e-903">`Action`) e todas as sobrecargas relacionadas são baseadas na ideia de que você deseja especificar ao que está vinculando, especificando um nome do controlador e um nome da ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-903">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-904">Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são especificados para você – o valor de `controller` e `action` fazem parte de *valores de ambiente* \*\*e de \*\* *valores*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-904">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="01d5e-905">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.</span><span class="sxs-lookup"><span data-stu-id="01d5e-905">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="01d5e-906">O roteamento tenta usar os valores em valores de ambiente para preencher informações que você não forneceu ao gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-906">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="01d5e-907">Usando uma rota como `{a}/{b}/{c}/{d}` e valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, o roteamento tem informações suficientes para gerar uma URL sem valores adicionais – uma vez que todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="01d5e-907">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="01d5e-908">Se você tiver adicionado o valor `{ d = Donovan }`, o valor `{ d = David }` será ignorado e o caminho de URL gerado será `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-908">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="01d5e-909">Caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="01d5e-909">URL paths are hierarchical.</span></span> <span data-ttu-id="01d5e-910">No exemplo acima, se você tiver adicionado o valor `{ c = Cheryl }`, ambos os valores `{ c = Carol, d = David }` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="01d5e-910">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="01d5e-911">Nesse caso, não teremos mais um valor para `d` e a geração de URL falhará.</span><span class="sxs-lookup"><span data-stu-id="01d5e-911">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="01d5e-912">Você precisaria especificar o valor desejado de `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-912">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="01d5e-913">Você pode esperar se deparar com esse problema com a rota padrão (`{controller}/{action}/{id?}`) – mas raramente encontrará esse comportamento na prática, pois `Url.Action` sempre especificará explicitamente um valor de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-913">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="01d5e-914">Sobrecargas maiores de `Url.Action` também usam um objeto adicional de *valores de rota* para fornecer valores para parâmetros de rota diferentes de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-914">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="01d5e-915">É mais comum ver isso com `id` como `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-915">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="01d5e-916">Por convenção, o objeto de *valores de rota* geralmente é um objeto de tipo anônimo, mas também pode ser um `IDictionary<>` ou um *objeto .NET simples*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-916">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="01d5e-917">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="01d5e-917">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="01d5e-918">Para criar uma URL absoluta, use uma sobrecarga que aceita um `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="01d5e-918">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="01d5e-919">Gerando URLs pela rota</span><span class="sxs-lookup"><span data-stu-id="01d5e-919">Generating URLs by route</span></span>

<span data-ttu-id="01d5e-920">O código acima demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-920">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="01d5e-921">`IUrlHelper` também fornece a família de métodos `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-921">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="01d5e-922">Esses métodos são semelhantes a `Url.Action`, mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-922">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="01d5e-923">O uso mais comum é especificar um nome de rota para usar uma rota específica para gerar a URL, geralmente *sem* especificar um nome de controlador ou de ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-923">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="01d5e-924">Gerar URLs em HTML</span><span class="sxs-lookup"><span data-stu-id="01d5e-924">Generating URLs in HTML</span></span>

<span data-ttu-id="01d5e-925">`IHtmlHelper` fornece o métodos `Html.BeginForm` e `Html.ActionLink` de `HtmlHelper` para gerar elementos `<form>` e `<a>` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-925">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="01d5e-926">Esses métodos usam o método `Url.Action` para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-926">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="01d5e-927">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="01d5e-927">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="01d5e-928">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-928">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="01d5e-929">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-929">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="01d5e-930">Consulte [Trabalhando com Formulários](../views/working-with-forms.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="01d5e-930">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="01d5e-931">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="01d5e-931">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="01d5e-932">Gerando URLS nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="01d5e-932">Generating URLS in Action Results</span></span>

<span data-ttu-id="01d5e-933">Os exemplos acima mostraram o uso de `IUrlHelper` em um controlador, enquanto o uso mais comum em um controlador é gerar uma URL como parte do resultado de uma ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-933">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="01d5e-934">As classes base `ControllerBase` e `Controller` fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-934">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="01d5e-935">Um uso típico é para redirecionar após aceitar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="01d5e-935">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="01d5e-936">Os métodos de fábrica dos resultados da ação seguem um padrão semelhante aos métodos em `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-936">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="01d5e-937">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="01d5e-937">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="01d5e-938">O roteamento convencional pode usar um tipo especial de definição de rota chamado *rota convencional dedicada*.</span><span class="sxs-lookup"><span data-stu-id="01d5e-938">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="01d5e-939">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-939">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="01d5e-940">Usando essas definições de rota, `Url.Action("Index", "Home")` gerará o caminho de URL `/` com a rota `default`, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="01d5e-940">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="01d5e-941">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-941">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="01d5e-942">Rotas convencionais dedicadas dependem de um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impeça que a rota seja "muito ambiciosa" com a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="01d5e-942">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="01d5e-943">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-943">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="01d5e-944">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="01d5e-944">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="01d5e-945">A geração de URL usando `blog` falhará porque os valores de `{ controller = Home, action = Index }` não correspondem a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-945">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="01d5e-946">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="01d5e-946">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="01d5e-947">Áreas</span><span class="sxs-lookup"><span data-stu-id="01d5e-947">Areas</span></span>

<span data-ttu-id="01d5e-948">[Áreas](areas.md) são um recurso do MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace de roteamento (para ações do controlador) e estrutura de pasta (para exibições) separada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-948">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="01d5e-949">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="01d5e-949">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="01d5e-950">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-950">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="01d5e-951">Esta seção aborda como o roteamento interage com as áreas. Consulte [Áreas](areas.md) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="01d5e-951">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="01d5e-952">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma *rota de área* para uma área chamada `Blog`:</span><span class="sxs-lookup"><span data-stu-id="01d5e-952">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="01d5e-953">Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a primeira rota produzirá os valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-953">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="01d5e-954">O valor de rota `area` é produzido por um valor padrão para `area`. De fato, a rota criada por `MapAreaRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="01d5e-954">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="01d5e-955">`MapAreaRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-955">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="01d5e-956">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-956">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="01d5e-957">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-957">Conventional routing is order-dependent.</span></span> <span data-ttu-id="01d5e-958">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="01d5e-958">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="01d5e-959">Usando o exemplo acima, os valores de rota corresponderiam à ação a seguir:</span><span class="sxs-lookup"><span data-stu-id="01d5e-959">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="01d5e-960">O `AreaAttribute` é o que indica que um controlador faz parte de uma área; dizemos que esse controlador está na área `Blog`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-960">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="01d5e-961">Controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** corresponderão quando o valor de rota `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-961">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="01d5e-962">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-962">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="01d5e-963">O namespace de cada controlador é mostrado aqui para fins de integridade – caso contrário, os controladores teriam um conflito de nomenclatura e gerariam um erro do compilador.</span><span class="sxs-lookup"><span data-stu-id="01d5e-963">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="01d5e-964">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="01d5e-964">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="01d5e-965">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-965">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="01d5e-966">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="01d5e-966">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-967">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="01d5e-967">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="01d5e-968">Ao executar uma ação dentro de uma área, o valor de rota para `area` estará disponível como um *valor de ambiente* para o roteamento usar para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-968">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="01d5e-969">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="01d5e-969">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="01d5e-970">Entendendo IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="01d5e-970">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="01d5e-971">Esta seção é uma análise aprofundada dos elementos internos da estrutura e de como o MVC escolhe uma ação para ser executada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-971">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="01d5e-972">Um aplicativo típico não precisará de um `IActionConstraint` personalizado</span><span class="sxs-lookup"><span data-stu-id="01d5e-972">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="01d5e-973">Provavelmente, você já usou `IActionConstraint` mesmo que não esteja familiarizado com a interface.</span><span class="sxs-lookup"><span data-stu-id="01d5e-973">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="01d5e-974">O atributo `[HttpGet]` e atributos `[Http-VERB]` semelhantes implementam `IActionConstraint` para limitar a execução de um método de ação.</span><span class="sxs-lookup"><span data-stu-id="01d5e-974">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="01d5e-975">Presumindo a rota convencional padrão, o caminho de URL `/Products/Edit` produziria os valores `{ controller = Products, action = Edit }`, que corresponderiam a **ambas** as ações mostradas aqui.</span><span class="sxs-lookup"><span data-stu-id="01d5e-975">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="01d5e-976">Na terminologia `IActionConstraint`, diríamos que essas duas ações são consideradas candidatas – uma vez que ambas correspondem aos dados da rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-976">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="01d5e-977">Quando for executado, `HttpGetAttribute` indicará que *Edit()* corresponde a *GET* e não corresponde a nenhum outro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="01d5e-977">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="01d5e-978">A ação `Edit(...)` não tem restrições definidas e, portanto, corresponderá a qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="01d5e-978">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="01d5e-979">Sendo assim, supondo um `POST`, `Edit(...)` será correspondente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-979">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="01d5e-980">Mas, para um `GET`, ambas as ações ainda podem corresponder – no entanto, uma ação com um `IActionConstraint` sempre é considerada *melhor* que uma ação sem.</span><span class="sxs-lookup"><span data-stu-id="01d5e-980">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="01d5e-981">Assim, como `Edit()` tem `[HttpGet]`, ela é considerada mais específica e será selecionada se as duas ações puderem corresponder.</span><span class="sxs-lookup"><span data-stu-id="01d5e-981">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="01d5e-982">Conceitualmente, `IActionConstraint` é uma forma de *sobrecarga*, mas em vez de uma sobrecarga de métodos com o mesmo nome, trata-se da sobrecarga entre ações que correspondem à mesma URL.</span><span class="sxs-lookup"><span data-stu-id="01d5e-982">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="01d5e-983">O roteamento de atributo também usa `IActionConstraint` e pode fazer com que ações de controladores diferentes sejam consideradas candidatas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-983">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="01d5e-984">Implementando IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="01d5e-984">Implementing IActionConstraint</span></span>

<span data-ttu-id="01d5e-985">A maneira mais simples de implementar um `IActionConstraint` é criar uma classe derivada de `System.Attribute` e colocá-la em suas ações e controladores.</span><span class="sxs-lookup"><span data-stu-id="01d5e-985">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="01d5e-986">O MVC descobrirá automaticamente qualquer `IActionConstraint` que for aplicado como atributo.</span><span class="sxs-lookup"><span data-stu-id="01d5e-986">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="01d5e-987">Você pode usar o modelo de aplicativo para aplicar restrições e, provavelmente, essa é a abordagem mais flexível, pois permite a você faça uma metaprogramação de como elas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-987">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="01d5e-988">No exemplo a seguir, uma restrição escolhe uma ação com base em um *código de país* dos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="01d5e-988">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="01d5e-989">O [exemplo completo no GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="01d5e-989">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="01d5e-990">Você é responsável por implementar o método `Accept` e por escolher uma "ordem" na qual a restrição deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="01d5e-990">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="01d5e-991">Nesse caso, o método `Accept` retorna `true` para indicar que a ação é correspondente quando o valor de rota `country` é correspondente.</span><span class="sxs-lookup"><span data-stu-id="01d5e-991">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="01d5e-992">Isso é diferente de um `RouteValueAttribute`, pois permite o fallback para uma ação não atribuída.</span><span class="sxs-lookup"><span data-stu-id="01d5e-992">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="01d5e-993">O exemplo mostra que se você definir uma ação `en-US`, um código de país como `fr-FR` fará o fallback para um controlador mais genérico que não tem `[CountrySpecific(...)]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="01d5e-993">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="01d5e-994">A propriedade `Order` decide de qual *estágio* a restrição faz parte.</span><span class="sxs-lookup"><span data-stu-id="01d5e-994">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="01d5e-995">Restrições de ação são executadas em grupos com base no `Order`.</span><span class="sxs-lookup"><span data-stu-id="01d5e-995">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="01d5e-996">Por exemplo, todos atributos de método HTTP fornecidos pela estrutura usam o mesmo valor de `Order` para que sejam executados no mesmo estágio.</span><span class="sxs-lookup"><span data-stu-id="01d5e-996">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="01d5e-997">Você pode ter tantos estágios quantos forem necessários para implementar suas políticas desejadas.</span><span class="sxs-lookup"><span data-stu-id="01d5e-997">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="01d5e-998">Para decidir o valor para `Order`, considere se sua restrição deve ou não deve ser aplicada antes de métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="01d5e-998">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="01d5e-999">Números inferiores são executados primeiro.</span><span class="sxs-lookup"><span data-stu-id="01d5e-999">Lower numbers run first.</span></span>

::: moniker-end
