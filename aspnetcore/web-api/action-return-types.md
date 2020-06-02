---
<span data-ttu-id="0d62a-101">title: tipos de retorno de ação do controlador em ASP.NET Core autor da API Web: scottaddie Descrição: Saiba mais sobre como usar os vários tipos de retorno do método de ação do controlador em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d62a-101">title: Controller action return types in ASP.NET Core web API author: scottaddie description: Learn about using the various controller action method return types in an ASP.NET Core web API.</span></span>
<span data-ttu-id="0d62a-102">MS. Author: scaddie MS. Custom: MVC MS. Date: 02/03/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="0d62a-102">ms.author: scaddie ms.custom: mvc ms.date: 02/03/2020 no-loc:</span></span>
- <span data-ttu-id="0d62a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0d62a-103">'Blazor'</span></span>
- <span data-ttu-id="0d62a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0d62a-104">'Identity'</span></span>
- <span data-ttu-id="0d62a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0d62a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="0d62a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0d62a-106">'Razor'</span></span>
- <span data-ttu-id="0d62a-107">' SignalR ' UID: Web-API/ação-retornar tipos</span><span class="sxs-lookup"><span data-stu-id="0d62a-107">'SignalR' uid: web-api/action-return-types</span></span>

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="0d62a-108">Tipos de retorno de ação do controlador em ASP.NET Core API Web</span><span class="sxs-lookup"><span data-stu-id="0d62a-108">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="0d62a-109">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0d62a-109">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0d62a-110">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d62a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0d62a-111">O ASP.NET Core oferece as seguintes opções para tipos de retorno de ação do controlador da API Web:</span><span class="sxs-lookup"><span data-stu-id="0d62a-111">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="0d62a-112">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="0d62a-112">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="0d62a-113">IActionResult</span><span class="sxs-lookup"><span data-stu-id="0d62a-113">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="0d62a-114">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="0d62a-114">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="0d62a-115">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="0d62a-115">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="0d62a-116">IActionResult</span><span class="sxs-lookup"><span data-stu-id="0d62a-116">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="0d62a-117">Este documento explica quando é mais adequado usar cada tipo de retorno.</span><span class="sxs-lookup"><span data-stu-id="0d62a-117">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="0d62a-118">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="0d62a-118">Specific type</span></span>

<span data-ttu-id="0d62a-119">A ação mais simples retorna um tipo de dados complexo ou primitivo (por exemplo, `string` ou um tipo de objeto personalizado).</span><span class="sxs-lookup"><span data-stu-id="0d62a-119">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="0d62a-120">Considere a seguinte ação, que retorna uma coleção de objetos `Product` personalizados:</span><span class="sxs-lookup"><span data-stu-id="0d62a-120">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="0d62a-121">Sem condições conhecidas contra as quais se proteger durante a execução da ação, retornar um tipo específico pode ser suficiente.</span><span class="sxs-lookup"><span data-stu-id="0d62a-121">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="0d62a-122">A ação anterior não aceita parâmetros, assim, validação de restrições de parâmetro não é necessária.</span><span class="sxs-lookup"><span data-stu-id="0d62a-122">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="0d62a-123">Quando vários tipos de retorno são possíveis, é comum misturar um <xref:Microsoft.AspNetCore.Mvc.ActionResult> tipo de retorno com o tipo de retorno primitivo ou complexo.</span><span class="sxs-lookup"><span data-stu-id="0d62a-123">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="0d62a-124">O [IActionResult](#iactionresult-type) ou [o \<T> ActionResult](#actionresultt-type) são necessários para acomodar esse tipo de ação.</span><span class="sxs-lookup"><span data-stu-id="0d62a-124">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="0d62a-125">Várias amostras de vários tipos de retorno são fornecidas neste documento.</span><span class="sxs-lookup"><span data-stu-id="0d62a-125">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="0d62a-126">Retornar IEnumerable \<T> ou IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="0d62a-126">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="0d62a-127">No ASP.NET Core 2,2 e anteriores, retornar <xref:System.Collections.Generic.IEnumerable%601> de uma ação resulta em uma iteração de coleção síncrona pelo serializador.</span><span class="sxs-lookup"><span data-stu-id="0d62a-127">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="0d62a-128">O resultado é o bloqueio de chamadas e um potencial para a privação do pool de threads.</span><span class="sxs-lookup"><span data-stu-id="0d62a-128">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="0d62a-129">Para ilustrar, imagine que o núcleo do Entity Framework (EF) está sendo usado para as necessidades de acesso a dados da API Web.</span><span class="sxs-lookup"><span data-stu-id="0d62a-129">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="0d62a-130">O tipo de retorno da ação a seguir é enumerado de forma síncrona durante a serialização:</span><span class="sxs-lookup"><span data-stu-id="0d62a-130">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="0d62a-131">Para evitar a enumeração síncrona e as esperas de bloqueio no banco de dados no ASP.NET Core 2,2 e anteriores, invoque `ToListAsync` :</span><span class="sxs-lookup"><span data-stu-id="0d62a-131">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="0d62a-132">No ASP.NET Core 3,0 e posterior, retornando `IAsyncEnumerable<T>` de uma ação:</span><span class="sxs-lookup"><span data-stu-id="0d62a-132">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="0d62a-133">Não resulta mais em iteração síncrona.</span><span class="sxs-lookup"><span data-stu-id="0d62a-133">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="0d62a-134">Torna-se tão eficiente quanto retornar <xref:System.Collections.Generic.IEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="0d62a-134">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="0d62a-135">ASP.NET Core 3,0 e posterior armazena em buffer o resultado da seguinte ação antes de fornecer ao serializador:</span><span class="sxs-lookup"><span data-stu-id="0d62a-135">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="0d62a-136">Considere declarar o tipo de retorno da assinatura de ação como `IAsyncEnumerable<T>` para garantir a iteração assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0d62a-136">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="0d62a-137">Por fim, o modo de iteração é baseado no tipo concreto subjacente que está sendo retornado.</span><span class="sxs-lookup"><span data-stu-id="0d62a-137">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="0d62a-138">O MVC armazena em buffer automaticamente qualquer tipo concreto que implemente `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-138">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="0d62a-139">Considere a seguinte ação, que retorna registros de produtos com preço de venda como `IEnumerable<Product>` :</span><span class="sxs-lookup"><span data-stu-id="0d62a-139">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="0d62a-140">O `IAsyncEnumerable<Product>` equivalente da ação anterior é:</span><span class="sxs-lookup"><span data-stu-id="0d62a-140">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="0d62a-141">As duas ações anteriores não são bloqueadas a partir do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0d62a-141">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="0d62a-142">Tipo IActionResult</span><span class="sxs-lookup"><span data-stu-id="0d62a-142">IActionResult type</span></span>

<span data-ttu-id="0d62a-143">O <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipo de retorno é apropriado quando vários `ActionResult` tipos de retorno são possíveis em uma ação.</span><span class="sxs-lookup"><span data-stu-id="0d62a-143">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="0d62a-144">Os tipos `ActionResult` representam vários códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="0d62a-144">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="0d62a-145">Qualquer classe não abstrata derivada de é `ActionResult` qualificada como um tipo de retorno válido.</span><span class="sxs-lookup"><span data-stu-id="0d62a-145">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="0d62a-146">Alguns tipos de retorno comuns nessa categoria são <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) e <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="0d62a-146">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="0d62a-147">Como alternativa, os métodos de conveniência na <xref:Microsoft.AspNetCore.Mvc.ControllerBase> classe podem ser usados para retornar `ActionResult` tipos de uma ação.</span><span class="sxs-lookup"><span data-stu-id="0d62a-147">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="0d62a-148">Por exemplo, `return BadRequest();` é uma forma abreviada de `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-148">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="0d62a-149">Como há vários tipos de retorno e caminhos nesse tipo de ação, liberal uso do [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) atributo é necessário.</span><span class="sxs-lookup"><span data-stu-id="0d62a-149">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="0d62a-150">Esse atributo produz detalhes de resposta mais descritivos para páginas de ajuda da API Web geradas por ferramentas como o [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="0d62a-150">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="0d62a-151">`[ProducesResponseType]` indica os tipos conhecidos e os códigos de status HTTP a serem retornados pela ação.</span><span class="sxs-lookup"><span data-stu-id="0d62a-151">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="0d62a-152">Ação síncrona</span><span class="sxs-lookup"><span data-stu-id="0d62a-152">Synchronous action</span></span>

<span data-ttu-id="0d62a-153">Considere a seguinte ação síncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="0d62a-153">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="0d62a-154">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="0d62a-154">In the preceding action:</span></span>

* <span data-ttu-id="0d62a-155">Um código de status 404 é retornado quando o produto representado pelo `id` não existe no armazenamento de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="0d62a-155">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="0d62a-156">O <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> método de conveniência é invocado como abreviação para `return new NotFoundResult();` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-156">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="0d62a-157">Um código de status 200 é retornado com o `Product` objeto quando o produto existe.</span><span class="sxs-lookup"><span data-stu-id="0d62a-157">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="0d62a-158">O <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> método de conveniência é invocado como abreviação para `return new OkObjectResult(product);` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="0d62a-159">Ação assíncrona</span><span class="sxs-lookup"><span data-stu-id="0d62a-159">Asynchronous action</span></span>

<span data-ttu-id="0d62a-160">Considere a seguinte ação assíncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="0d62a-160">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="0d62a-161">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="0d62a-161">In the preceding action:</span></span>

* <span data-ttu-id="0d62a-162">Um código de status 400 é retornado quando a descrição do produto contém "widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="0d62a-162">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="0d62a-163">O <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> método de conveniência é invocado como abreviação para `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-163">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="0d62a-164">Um código de status 201 é gerado pelo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> método de conveniência quando um produto é criado.</span><span class="sxs-lookup"><span data-stu-id="0d62a-164">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="0d62a-165">Uma alternativa para chamar `CreatedAtAction` é `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-165">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="0d62a-166">Nesse caminho de código, o `Product` objeto é fornecido no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="0d62a-166">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="0d62a-167">`Location`É fornecido um cabeçalho de resposta contendo a URL do produto recém-criado.</span><span class="sxs-lookup"><span data-stu-id="0d62a-167">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="0d62a-168">Por exemplo, o modelo a seguir indica que as solicitações devem incluir as propriedades `Name` e `Description`.</span><span class="sxs-lookup"><span data-stu-id="0d62a-168">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="0d62a-169">A falha em fornecer `Name` e `Description` na solicitação faz com que a validação do modelo falhe.</span><span class="sxs-lookup"><span data-stu-id="0d62a-169">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0d62a-170">Se o [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo no ASP.NET Core 2,1 ou posterior for aplicado, os erros de validação de modelo resultarão em um código de status 400.</span><span class="sxs-lookup"><span data-stu-id="0d62a-170">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="0d62a-171">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="0d62a-171">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="0d62a-172">Tipo de ActionResult \<T></span><span class="sxs-lookup"><span data-stu-id="0d62a-172">ActionResult\<T> type</span></span>

<span data-ttu-id="0d62a-173">ASP.NET Core 2,1 introduziu o tipo de retorno [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) para ações do controlador da API Web.</span><span class="sxs-lookup"><span data-stu-id="0d62a-173">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="0d62a-174">Ele permite que você retorne um tipo derivado de <xref:Microsoft.AspNetCore.Mvc.ActionResult> ou retorne um [tipo específico](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="0d62a-174">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="0d62a-175">`ActionResult<T>` oferece os seguintes benefícios em relação ao [tipo IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="0d62a-175">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="0d62a-176">A [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) Propriedade do atributo `Type` pode ser excluída.</span><span class="sxs-lookup"><span data-stu-id="0d62a-176">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="0d62a-177">Por exemplo, `[ProducesResponseType(200, Type = typeof(Product))]` é simplificado para `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="0d62a-177">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="0d62a-178">O tipo de retorno esperado da ação é inferido do `T` em `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d62a-178">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="0d62a-179">[Operadores de conversão implícita](/dotnet/csharp/language-reference/keywords/implicit) são compatíveis com a conversão de `T` e `ActionResult` em `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d62a-179">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="0d62a-180">`T`Converte para <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , que significa `return new ObjectResult(T);` é simplificado para `return T;` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-180">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="0d62a-181">C# não dá suporte a operadores de conversão implícita em interfaces.</span><span class="sxs-lookup"><span data-stu-id="0d62a-181">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="0d62a-182">Consequentemente, a conversão da interface para um tipo concreto é necessário para usar `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d62a-182">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="0d62a-183">Por exemplo, o uso de `IEnumerable` no exemplo a seguir não funciona:</span><span class="sxs-lookup"><span data-stu-id="0d62a-183">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="0d62a-184">Uma opção para corrigir o código anterior é retornar a `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="0d62a-184">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="0d62a-185">A maioria das ações tem um tipo de retorno específico.</span><span class="sxs-lookup"><span data-stu-id="0d62a-185">Most actions have a specific return type.</span></span> <span data-ttu-id="0d62a-186">Condições inesperadas podem ocorrer durante a execução da ação, caso em que o tipo específico não é retornado.</span><span class="sxs-lookup"><span data-stu-id="0d62a-186">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="0d62a-187">Por exemplo, o parâmetro de entrada de uma ação pode falhar na validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="0d62a-187">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="0d62a-188">Nesse caso, é comum retornar o tipo `ActionResult` adequado, em vez do tipo específico.</span><span class="sxs-lookup"><span data-stu-id="0d62a-188">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="0d62a-189">Ação síncrona</span><span class="sxs-lookup"><span data-stu-id="0d62a-189">Synchronous action</span></span>

<span data-ttu-id="0d62a-190">Considere uma ação síncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="0d62a-190">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="0d62a-191">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="0d62a-191">In the preceding action:</span></span>

* <span data-ttu-id="0d62a-192">Um código de status 404 é retornado quando o produto não existe no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0d62a-192">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="0d62a-193">Um código de status 200 é retornado com o `Product` objeto correspondente quando o produto existe.</span><span class="sxs-lookup"><span data-stu-id="0d62a-193">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="0d62a-194">Antes ASP.NET Core 2,1, a `return product;` linha tinha que ser `return Ok(product);` .</span><span class="sxs-lookup"><span data-stu-id="0d62a-194">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="0d62a-195">Ação assíncrona</span><span class="sxs-lookup"><span data-stu-id="0d62a-195">Asynchronous action</span></span>

<span data-ttu-id="0d62a-196">Considere uma ação assíncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="0d62a-196">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="0d62a-197">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="0d62a-197">In the preceding action:</span></span>

* <span data-ttu-id="0d62a-198">Um código de status 400 ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) é retornado pelo tempo de execução de ASP.NET Core quando:</span><span class="sxs-lookup"><span data-stu-id="0d62a-198">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="0d62a-199">O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo foi aplicado e a validação do modelo falha.</span><span class="sxs-lookup"><span data-stu-id="0d62a-199">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="0d62a-200">A descrição do produto contém "Widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="0d62a-200">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="0d62a-201">Um código de status 201 é gerado pelo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> método quando um produto é criado.</span><span class="sxs-lookup"><span data-stu-id="0d62a-201">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="0d62a-202">Nesse caminho de código, o `Product` objeto é fornecido no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="0d62a-202">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="0d62a-203">`Location`É fornecido um cabeçalho de resposta contendo a URL do produto recém-criado.</span><span class="sxs-lookup"><span data-stu-id="0d62a-203">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0d62a-204">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0d62a-204">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
