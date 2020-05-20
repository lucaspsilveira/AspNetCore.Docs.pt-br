---
<span data-ttu-id="419ee-101">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-102">'Blazor'</span></span>
- <span data-ttu-id="419ee-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-103">'Identity'</span></span>
- <span data-ttu-id="419ee-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-105">'Razor'</span></span>
- <span data-ttu-id="419ee-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="419ee-107">JsonPatch na API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="419ee-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="419ee-108">Por [Tom Dykstra](https://github.com/tdykstra) e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="419ee-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="419ee-109">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="419ee-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="419ee-110">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="419ee-110">Package installation</span></span>

<span data-ttu-id="419ee-111">Para habilitar o suporte a patch JSON em seu aplicativo, conclua as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="419ee-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="419ee-112">Instale o [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="419ee-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="419ee-113">Atualize o método do projeto `Startup.ConfigureServices` para chamar <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="419ee-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="419ee-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="419ee-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="419ee-115">`AddNewtonsoftJson`é compatível com os métodos de registro do serviço MVC:</span><span class="sxs-lookup"><span data-stu-id="419ee-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="419ee-116">Patch JSON, AddNewtonsoftJson e System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="419ee-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="419ee-117">`AddNewtonsoftJson`substitui os `System.Text.Json` formatadores de entrada e saída baseados em dados usados para formatar **todo** o conteúdo JSON.</span><span class="sxs-lookup"><span data-stu-id="419ee-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="419ee-118">Para adicionar suporte ao patch JSON usando `Newtonsoft.Json` o, deixando os outros formatadores inalterados, atualize o método do projeto da `Startup.ConfigureServices` seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="419ee-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="419ee-119">O código anterior requer o `Microsoft.AspNetCore.Mvc.NewtonsoftJson` pacote e as seguintes `using` instruções:</span><span class="sxs-lookup"><span data-stu-id="419ee-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="419ee-120">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="419ee-120">PATCH HTTP request method</span></span>

<span data-ttu-id="419ee-121">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="419ee-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="419ee-122">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="419ee-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="419ee-123">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="419ee-123">JSON Patch</span></span>

<span data-ttu-id="419ee-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="419ee-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="419ee-125">Um documento de JSON Patch tem uma matriz de *operações*.</span><span class="sxs-lookup"><span data-stu-id="419ee-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="419ee-126">Cada operação identifica um tipo específico de alteração.</span><span class="sxs-lookup"><span data-stu-id="419ee-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="419ee-127">Exemplos de tais alterações incluem adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="419ee-128">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de patch JSON para o recurso e o resultado da aplicação das operações de patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="419ee-129">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="419ee-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="419ee-130">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="419ee-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="419ee-131">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="419ee-131">In the preceding JSON:</span></span>

* <span data-ttu-id="419ee-132">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="419ee-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="419ee-133">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="419ee-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="419ee-134">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="419ee-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="419ee-135">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="419ee-135">Resource after patch</span></span>

<span data-ttu-id="419ee-136">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="419ee-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="419ee-137">As alterações feitas pela aplicação de um documento de patch JSON a um recurso são atômicas.</span><span class="sxs-lookup"><span data-stu-id="419ee-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="419ee-138">Se qualquer operação na lista falhar, nenhuma operação na lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="419ee-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="419ee-139">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="419ee-139">Path syntax</span></span>

<span data-ttu-id="419ee-140">A propriedade [path](https://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="419ee-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="419ee-141">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="419ee-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="419ee-142">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="419ee-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="419ee-143">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="419ee-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="419ee-144">Para `add` o final de uma matriz, use um hífen ( `-` ) em vez de um número de índice: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="419ee-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="419ee-145">Operações</span><span class="sxs-lookup"><span data-stu-id="419ee-145">Operations</span></span>

<span data-ttu-id="419ee-146">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="419ee-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="419ee-147">Operação</span><span class="sxs-lookup"><span data-stu-id="419ee-147">Operation</span></span>  | <span data-ttu-id="419ee-148">Observações</span><span class="sxs-lookup"><span data-stu-id="419ee-148">Notes</span></span> |
|---
<span data-ttu-id="419ee-149">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-150">'Blazor'</span></span>
- <span data-ttu-id="419ee-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-151">'Identity'</span></span>
- <span data-ttu-id="419ee-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-153">'Razor'</span></span>
- <span data-ttu-id="419ee-154">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-155">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-156">'Blazor'</span></span>
- <span data-ttu-id="419ee-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-157">'Identity'</span></span>
- <span data-ttu-id="419ee-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-159">'Razor'</span></span>
- <span data-ttu-id="419ee-160">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-161">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-162">'Blazor'</span></span>
- <span data-ttu-id="419ee-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-163">'Identity'</span></span>
- <span data-ttu-id="419ee-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-165">'Razor'</span></span>
- <span data-ttu-id="419ee-166">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-166">'SignalR' uid:</span></span> 

<span data-ttu-id="419ee-167">------|---
Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-168">'Blazor'</span></span>
- <span data-ttu-id="419ee-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-169">'Identity'</span></span>
- <span data-ttu-id="419ee-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-171">'Razor'</span></span>
- <span data-ttu-id="419ee-172">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-173">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-174">'Blazor'</span></span>
- <span data-ttu-id="419ee-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-175">'Identity'</span></span>
- <span data-ttu-id="419ee-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-177">'Razor'</span></span>
- <span data-ttu-id="419ee-178">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-179">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-180">'Blazor'</span></span>
- <span data-ttu-id="419ee-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-181">'Identity'</span></span>
- <span data-ttu-id="419ee-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-183">'Razor'</span></span>
- <span data-ttu-id="419ee-184">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-185">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-186">'Blazor'</span></span>
- <span data-ttu-id="419ee-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-187">'Identity'</span></span>
- <span data-ttu-id="419ee-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-189">'Razor'</span></span>
- <span data-ttu-id="419ee-190">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-191">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-192">'Blazor'</span></span>
- <span data-ttu-id="419ee-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-193">'Identity'</span></span>
- <span data-ttu-id="419ee-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-195">'Razor'</span></span>
- <span data-ttu-id="419ee-196">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-197">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-198">'Blazor'</span></span>
- <span data-ttu-id="419ee-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-199">'Identity'</span></span>
- <span data-ttu-id="419ee-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-201">'Razor'</span></span>
- <span data-ttu-id="419ee-202">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-203">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-204">'Blazor'</span></span>
- <span data-ttu-id="419ee-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-205">'Identity'</span></span>
- <span data-ttu-id="419ee-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-207">'Razor'</span></span>
- <span data-ttu-id="419ee-208">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-209">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-210">'Blazor'</span></span>
- <span data-ttu-id="419ee-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-211">'Identity'</span></span>
- <span data-ttu-id="419ee-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-213">'Razor'</span></span>
- <span data-ttu-id="419ee-214">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-215">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-216">'Blazor'</span></span>
- <span data-ttu-id="419ee-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-217">'Identity'</span></span>
- <span data-ttu-id="419ee-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-219">'Razor'</span></span>
- <span data-ttu-id="419ee-220">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-221">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-222">'Blazor'</span></span>
- <span data-ttu-id="419ee-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-223">'Identity'</span></span>
- <span data-ttu-id="419ee-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-225">'Razor'</span></span>
- <span data-ttu-id="419ee-226">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-227">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-228">'Blazor'</span></span>
- <span data-ttu-id="419ee-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-229">'Identity'</span></span>
- <span data-ttu-id="419ee-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-231">'Razor'</span></span>
- <span data-ttu-id="419ee-232">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-233">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-234">'Blazor'</span></span>
- <span data-ttu-id="419ee-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-235">'Identity'</span></span>
- <span data-ttu-id="419ee-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-237">'Razor'</span></span>
- <span data-ttu-id="419ee-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-239">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-240">'Blazor'</span></span>
- <span data-ttu-id="419ee-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-241">'Identity'</span></span>
- <span data-ttu-id="419ee-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-243">'Razor'</span></span>
- <span data-ttu-id="419ee-244">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-245">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-246">'Blazor'</span></span>
- <span data-ttu-id="419ee-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-247">'Identity'</span></span>
- <span data-ttu-id="419ee-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-249">'Razor'</span></span>
- <span data-ttu-id="419ee-250">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-250">'SignalR' uid:</span></span> 

<span data-ttu-id="419ee-251">----------------| | `add`     | Adicione uma propriedade ou um elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="419ee-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="419ee-252">Para propriedade existente: definir valor. | | `remove`  | Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="419ee-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="419ee-253">| | `replace` | O mesmo é `remove` seguido pelo `add` mesmo local.</span><span class="sxs-lookup"><span data-stu-id="419ee-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="419ee-254">| | `move`    | O mesmo que `remove` da origem seguida pelo `add` destino usando o valor da origem.</span><span class="sxs-lookup"><span data-stu-id="419ee-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="419ee-255">| | `copy`    | O mesmo que o `add` destino usando o valor da origem.</span><span class="sxs-lookup"><span data-stu-id="419ee-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="419ee-256">| | `test`    | Retornar código de status de êxito se valor em `path` = fornecido `value` . |</span><span class="sxs-lookup"><span data-stu-id="419ee-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="419ee-257">Patch JSON no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="419ee-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="419ee-258">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="419ee-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="419ee-259">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="419ee-259">Action method code</span></span>

<span data-ttu-id="419ee-260">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="419ee-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="419ee-261">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="419ee-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="419ee-262">Aceita um `JsonPatchDocument<T>` , normalmente com `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="419ee-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="419ee-263">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="419ee-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="419ee-264">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="419ee-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="419ee-265">Este código do aplicativo de exemplo funciona com o seguinte `Customer` modelo:</span><span class="sxs-lookup"><span data-stu-id="419ee-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="419ee-266">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="419ee-266">The sample action method:</span></span>

* <span data-ttu-id="419ee-267">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="419ee-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="419ee-268">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-268">Applies the patch.</span></span>
* <span data-ttu-id="419ee-269">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="419ee-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="419ee-270">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="419ee-271">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="419ee-271">Model state</span></span>

<span data-ttu-id="419ee-272">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="419ee-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="419ee-273">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="419ee-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="419ee-274">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="419ee-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="419ee-275">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="419ee-275">Dynamic objects</span></span>

<span data-ttu-id="419ee-276">O exemplo do método de ação a seguir mostra como aplicar um patch a um objeto dinâmico:</span><span class="sxs-lookup"><span data-stu-id="419ee-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="419ee-277">A operação add</span><span class="sxs-lookup"><span data-stu-id="419ee-277">The add operation</span></span>

* <span data-ttu-id="419ee-278">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="419ee-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="419ee-279">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="419ee-280">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="419ee-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="419ee-281">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="419ee-282">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="419ee-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="419ee-283">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="419ee-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="419ee-284">A operação remove</span><span class="sxs-lookup"><span data-stu-id="419ee-284">The remove operation</span></span>

* <span data-ttu-id="419ee-285">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="419ee-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="419ee-286">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="419ee-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="419ee-287">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="419ee-288">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="419ee-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="419ee-289">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="419ee-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="419ee-290">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="419ee-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="419ee-291">O documento de exemplo de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="419ee-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="419ee-292">A operação replace</span><span class="sxs-lookup"><span data-stu-id="419ee-292">The replace operation</span></span>

<span data-ttu-id="419ee-293">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="419ee-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="419ee-294">O documento de patch de exemplo a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo `Order` objeto:</span><span class="sxs-lookup"><span data-stu-id="419ee-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="419ee-295">A operação move</span><span class="sxs-lookup"><span data-stu-id="419ee-295">The move operation</span></span>

* <span data-ttu-id="419ee-296">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="419ee-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="419ee-297">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="419ee-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="419ee-298">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="419ee-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="419ee-299">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="419ee-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="419ee-300">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="419ee-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="419ee-301">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="419ee-301">The following sample patch document:</span></span>

* <span data-ttu-id="419ee-302">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="419ee-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="419ee-303">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="419ee-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="419ee-304">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="419ee-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="419ee-305">A operação copy</span><span class="sxs-lookup"><span data-stu-id="419ee-305">The copy operation</span></span>

<span data-ttu-id="419ee-306">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="419ee-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="419ee-307">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="419ee-307">The following sample patch document:</span></span>

* <span data-ttu-id="419ee-308">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="419ee-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="419ee-309">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="419ee-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="419ee-310">A operação test</span><span class="sxs-lookup"><span data-stu-id="419ee-310">The test operation</span></span>

<span data-ttu-id="419ee-311">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="419ee-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="419ee-312">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="419ee-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="419ee-313">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="419ee-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="419ee-314">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="419ee-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="419ee-315">Obter o código</span><span class="sxs-lookup"><span data-stu-id="419ee-315">Get the code</span></span>

<span data-ttu-id="419ee-316">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="419ee-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="419ee-317">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="419ee-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="419ee-318">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="419ee-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="419ee-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="419ee-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="419ee-320">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="419ee-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="419ee-321">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="419ee-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="419ee-322">Corpo: Copie e cole um dos exemplos de documento de patch JSON da pasta de projeto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="419ee-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="419ee-323">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="419ee-323">Additional resources</span></span>

* [<span data-ttu-id="419ee-324">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="419ee-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="419ee-325">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="419ee-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="419ee-326">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="419ee-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="419ee-327">[Documentação do JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="419ee-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="419ee-328">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="419ee-329">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="419ee-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="419ee-330">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="419ee-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="419ee-331">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="419ee-331">PATCH HTTP request method</span></span>

<span data-ttu-id="419ee-332">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="419ee-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="419ee-333">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="419ee-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="419ee-334">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="419ee-334">JSON Patch</span></span>

<span data-ttu-id="419ee-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="419ee-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="419ee-336">Um documento de JSON Patch tem uma matriz de *operações*.</span><span class="sxs-lookup"><span data-stu-id="419ee-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="419ee-337">Cada operação identifica um tipo específico de alteração, como adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="419ee-338">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de JSON Patch para o recurso e o resultado da aplicação de operações patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="419ee-339">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="419ee-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="419ee-340">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="419ee-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="419ee-341">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="419ee-341">In the preceding JSON:</span></span>

* <span data-ttu-id="419ee-342">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="419ee-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="419ee-343">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="419ee-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="419ee-344">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="419ee-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="419ee-345">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="419ee-345">Resource after patch</span></span>

<span data-ttu-id="419ee-346">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="419ee-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="419ee-347">As alterações feitas ao aplicar um documento JSON Patch a um recurso são atômicas: se alguma operação da lista falhar, nenhuma operação da lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="419ee-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="419ee-348">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="419ee-348">Path syntax</span></span>

<span data-ttu-id="419ee-349">A propriedade [path](https://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="419ee-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="419ee-350">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="419ee-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="419ee-351">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="419ee-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="419ee-352">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="419ee-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="419ee-353">Para `add` ao final de uma matriz, use um hífen (-) ao invés de um número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="419ee-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="419ee-354">Operações</span><span class="sxs-lookup"><span data-stu-id="419ee-354">Operations</span></span>

<span data-ttu-id="419ee-355">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="419ee-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="419ee-356">Operação</span><span class="sxs-lookup"><span data-stu-id="419ee-356">Operation</span></span>  | <span data-ttu-id="419ee-357">Observações</span><span class="sxs-lookup"><span data-stu-id="419ee-357">Notes</span></span> |
|---
<span data-ttu-id="419ee-358">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-359">'Blazor'</span></span>
- <span data-ttu-id="419ee-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-360">'Identity'</span></span>
- <span data-ttu-id="419ee-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-362">'Razor'</span></span>
- <span data-ttu-id="419ee-363">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-364">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-365">'Blazor'</span></span>
- <span data-ttu-id="419ee-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-366">'Identity'</span></span>
- <span data-ttu-id="419ee-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-368">'Razor'</span></span>
- <span data-ttu-id="419ee-369">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-370">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-371">'Blazor'</span></span>
- <span data-ttu-id="419ee-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-372">'Identity'</span></span>
- <span data-ttu-id="419ee-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-374">'Razor'</span></span>
- <span data-ttu-id="419ee-375">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-375">'SignalR' uid:</span></span> 

<span data-ttu-id="419ee-376">------|---
Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-377">'Blazor'</span></span>
- <span data-ttu-id="419ee-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-378">'Identity'</span></span>
- <span data-ttu-id="419ee-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-380">'Razor'</span></span>
- <span data-ttu-id="419ee-381">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-382">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-383">'Blazor'</span></span>
- <span data-ttu-id="419ee-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-384">'Identity'</span></span>
- <span data-ttu-id="419ee-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-386">'Razor'</span></span>
- <span data-ttu-id="419ee-387">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-388">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-389">'Blazor'</span></span>
- <span data-ttu-id="419ee-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-390">'Identity'</span></span>
- <span data-ttu-id="419ee-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-392">'Razor'</span></span>
- <span data-ttu-id="419ee-393">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-394">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-395">'Blazor'</span></span>
- <span data-ttu-id="419ee-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-396">'Identity'</span></span>
- <span data-ttu-id="419ee-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-398">'Razor'</span></span>
- <span data-ttu-id="419ee-399">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-400">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-401">'Blazor'</span></span>
- <span data-ttu-id="419ee-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-402">'Identity'</span></span>
- <span data-ttu-id="419ee-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-404">'Razor'</span></span>
- <span data-ttu-id="419ee-405">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-406">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-407">'Blazor'</span></span>
- <span data-ttu-id="419ee-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-408">'Identity'</span></span>
- <span data-ttu-id="419ee-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-410">'Razor'</span></span>
- <span data-ttu-id="419ee-411">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-412">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-413">'Blazor'</span></span>
- <span data-ttu-id="419ee-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-414">'Identity'</span></span>
- <span data-ttu-id="419ee-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-416">'Razor'</span></span>
- <span data-ttu-id="419ee-417">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-418">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-419">'Blazor'</span></span>
- <span data-ttu-id="419ee-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-420">'Identity'</span></span>
- <span data-ttu-id="419ee-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-422">'Razor'</span></span>
- <span data-ttu-id="419ee-423">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-424">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-425">'Blazor'</span></span>
- <span data-ttu-id="419ee-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-426">'Identity'</span></span>
- <span data-ttu-id="419ee-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-428">'Razor'</span></span>
- <span data-ttu-id="419ee-429">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-430">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-431">'Blazor'</span></span>
- <span data-ttu-id="419ee-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-432">'Identity'</span></span>
- <span data-ttu-id="419ee-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-434">'Razor'</span></span>
- <span data-ttu-id="419ee-435">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-436">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-437">'Blazor'</span></span>
- <span data-ttu-id="419ee-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-438">'Identity'</span></span>
- <span data-ttu-id="419ee-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-440">'Razor'</span></span>
- <span data-ttu-id="419ee-441">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-442">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-443">'Blazor'</span></span>
- <span data-ttu-id="419ee-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-444">'Identity'</span></span>
- <span data-ttu-id="419ee-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-446">'Razor'</span></span>
- <span data-ttu-id="419ee-447">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-448">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-449">'Blazor'</span></span>
- <span data-ttu-id="419ee-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-450">'Identity'</span></span>
- <span data-ttu-id="419ee-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-452">'Razor'</span></span>
- <span data-ttu-id="419ee-453">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="419ee-454">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="419ee-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="419ee-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="419ee-455">'Blazor'</span></span>
- <span data-ttu-id="419ee-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="419ee-456">'Identity'</span></span>
- <span data-ttu-id="419ee-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="419ee-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="419ee-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="419ee-458">'Razor'</span></span>
- <span data-ttu-id="419ee-459">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="419ee-459">'SignalR' uid:</span></span> 

<span data-ttu-id="419ee-460">----------------| | `add`     | Adicione uma propriedade ou um elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="419ee-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="419ee-461">Para propriedade existente: definir valor. | | `remove`  | Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="419ee-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="419ee-462">| | `replace` | O mesmo é `remove` seguido pelo `add` mesmo local.</span><span class="sxs-lookup"><span data-stu-id="419ee-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="419ee-463">| | `move`    | O mesmo que `remove` da origem seguida pelo `add` destino usando o valor da origem.</span><span class="sxs-lookup"><span data-stu-id="419ee-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="419ee-464">| | `copy`    | O mesmo que o `add` destino usando o valor da origem.</span><span class="sxs-lookup"><span data-stu-id="419ee-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="419ee-465">| | `test`    | Retornar código de status de êxito se valor em `path` = fornecido `value` . |</span><span class="sxs-lookup"><span data-stu-id="419ee-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="419ee-466">JsonPatch em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="419ee-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="419ee-467">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="419ee-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="419ee-468">O pacote está incluso no metapacote [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="419ee-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="419ee-469">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="419ee-469">Action method code</span></span>

<span data-ttu-id="419ee-470">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="419ee-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="419ee-471">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="419ee-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="419ee-472">Aceita um `JsonPatchDocument<T>` , normalmente com `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="419ee-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="419ee-473">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="419ee-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="419ee-474">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="419ee-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="419ee-475">Esse código do aplicativo de exemplo funciona com o seguinte modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="419ee-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="419ee-476">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="419ee-476">The sample action method:</span></span>

* <span data-ttu-id="419ee-477">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="419ee-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="419ee-478">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-478">Applies the patch.</span></span>
* <span data-ttu-id="419ee-479">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="419ee-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="419ee-480">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="419ee-481">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="419ee-481">Model state</span></span>

<span data-ttu-id="419ee-482">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="419ee-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="419ee-483">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="419ee-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="419ee-484">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="419ee-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="419ee-485">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="419ee-485">Dynamic objects</span></span>

<span data-ttu-id="419ee-486">O exemplo de método de ação a seguir mostra como aplicar um patch a um objeto dinâmico.</span><span class="sxs-lookup"><span data-stu-id="419ee-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="419ee-487">A operação add</span><span class="sxs-lookup"><span data-stu-id="419ee-487">The add operation</span></span>

* <span data-ttu-id="419ee-488">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="419ee-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="419ee-489">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="419ee-490">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="419ee-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="419ee-491">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="419ee-492">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="419ee-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="419ee-493">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="419ee-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="419ee-494">A operação remove</span><span class="sxs-lookup"><span data-stu-id="419ee-494">The remove operation</span></span>

* <span data-ttu-id="419ee-495">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="419ee-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="419ee-496">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="419ee-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="419ee-497">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="419ee-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="419ee-498">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="419ee-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="419ee-499">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="419ee-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="419ee-500">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="419ee-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="419ee-501">O exemplo de documento de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="419ee-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="419ee-502">A operação replace</span><span class="sxs-lookup"><span data-stu-id="419ee-502">The replace operation</span></span>

<span data-ttu-id="419ee-503">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="419ee-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="419ee-504">O exemplo de documento de patch a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="419ee-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="419ee-505">A operação move</span><span class="sxs-lookup"><span data-stu-id="419ee-505">The move operation</span></span>

* <span data-ttu-id="419ee-506">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="419ee-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="419ee-507">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="419ee-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="419ee-508">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="419ee-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="419ee-509">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="419ee-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="419ee-510">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="419ee-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="419ee-511">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="419ee-511">The following sample patch document:</span></span>

* <span data-ttu-id="419ee-512">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="419ee-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="419ee-513">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="419ee-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="419ee-514">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="419ee-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="419ee-515">A operação copy</span><span class="sxs-lookup"><span data-stu-id="419ee-515">The copy operation</span></span>

<span data-ttu-id="419ee-516">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="419ee-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="419ee-517">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="419ee-517">The following sample patch document:</span></span>

* <span data-ttu-id="419ee-518">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="419ee-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="419ee-519">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="419ee-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="419ee-520">A operação test</span><span class="sxs-lookup"><span data-stu-id="419ee-520">The test operation</span></span>

<span data-ttu-id="419ee-521">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="419ee-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="419ee-522">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="419ee-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="419ee-523">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="419ee-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="419ee-524">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="419ee-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="419ee-525">Obter o código</span><span class="sxs-lookup"><span data-stu-id="419ee-525">Get the code</span></span>

<span data-ttu-id="419ee-526">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="419ee-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="419ee-527">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="419ee-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="419ee-528">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="419ee-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="419ee-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="419ee-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="419ee-530">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="419ee-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="419ee-531">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="419ee-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="419ee-532">Corpo: Copie e cole um dos exemplos de documento de patch JSON da pasta de projeto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="419ee-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="419ee-533">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="419ee-533">Additional resources</span></span>

* [<span data-ttu-id="419ee-534">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="419ee-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="419ee-535">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="419ee-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="419ee-536">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="419ee-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="419ee-537">[Documentação do JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="419ee-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="419ee-538">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="419ee-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="419ee-539">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="419ee-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
