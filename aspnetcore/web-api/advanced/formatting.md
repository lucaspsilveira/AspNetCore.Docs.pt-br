---
<span data-ttu-id="be555-101">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-102">'Blazor'</span></span>
- <span data-ttu-id="be555-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-103">'Identity'</span></span>
- <span data-ttu-id="be555-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-105">'Razor'</span></span>
- <span data-ttu-id="be555-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="be555-107">Formatar dados de resposta na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be555-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="be555-108">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="be555-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="be555-109">ASP.NET Core MVC tem suporte para formatar dados de resposta.</span><span class="sxs-lookup"><span data-stu-id="be555-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="be555-110">Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado do cliente.</span><span class="sxs-lookup"><span data-stu-id="be555-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="be555-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be555-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="be555-112">Resultados de ação específica de formato</span><span class="sxs-lookup"><span data-stu-id="be555-112">Format-specific Action Results</span></span>

<span data-ttu-id="be555-113">Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="be555-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="be555-114">As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente.</span><span class="sxs-lookup"><span data-stu-id="be555-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="be555-115">Por exemplo, retornando `JsonResult` retorna dados formatados para JSON.</span><span class="sxs-lookup"><span data-stu-id="be555-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="be555-116">Retornar `ContentResult` ou uma cadeia de caracteres retorna dados de cadeia de caracteres formatados com texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="be555-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="be555-117">Uma ação não é necessária para retornar qualquer tipo específico.</span><span class="sxs-lookup"><span data-stu-id="be555-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="be555-118">ASP.NET Core dá suporte a qualquer valor de retorno de objeto.</span><span class="sxs-lookup"><span data-stu-id="be555-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="be555-119">Os resultados de ações que retornam objetos que não são <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipos são serializados usando a <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementação apropriada.</span><span class="sxs-lookup"><span data-stu-id="be555-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="be555-120">Para obter mais informações, consulte <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="be555-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="be555-121">O método auxiliar interno <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> retorna dados formatados em JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="be555-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="be555-122">O download de exemplo retorna a lista de autores.</span><span class="sxs-lookup"><span data-stu-id="be555-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="be555-123">Usando as ferramentas de desenvolvedor de navegador F12 ou o [postmaster](https://www.getpostman.com/tools) com o código anterior:</span><span class="sxs-lookup"><span data-stu-id="be555-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="be555-124">O cabeçalho de resposta que contém o **tipo de conteúdo:** `application/json; charset=utf-8` é exibido.</span><span class="sxs-lookup"><span data-stu-id="be555-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="be555-125">Os cabeçalhos de solicitação são exibidos.</span><span class="sxs-lookup"><span data-stu-id="be555-125">The request headers are displayed.</span></span> <span data-ttu-id="be555-126">Por exemplo, o `Accept` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="be555-126">For example, the `Accept` header.</span></span> <span data-ttu-id="be555-127">O `Accept` cabeçalho é ignorado pelo código anterior.</span><span class="sxs-lookup"><span data-stu-id="be555-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="be555-128">Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="be555-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="be555-129">No código anterior, o `Content-Type` retornado é `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="be555-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="be555-130">O retorno de uma cadeia de caracteres é entregue `Content-Type` de `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="be555-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="be555-131">Para ações com vários tipos de retorno, retorne `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="be555-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="be555-132">Por exemplo, retornando códigos de status HTTP diferentes com base no resultado das operações executadas.</span><span class="sxs-lookup"><span data-stu-id="be555-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="be555-133">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="be555-133">Content negotiation</span></span>

<span data-ttu-id="be555-134">A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="be555-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="be555-135">O formato padrão usado pelo ASP.NET Core é [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="be555-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="be555-136">A negociação de conteúdo é:</span><span class="sxs-lookup"><span data-stu-id="be555-136">Content negotiation is:</span></span>

* <span data-ttu-id="be555-137">Implementado pelo <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="be555-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="be555-138">Incorporados aos resultados da ação específica do código de status retornados dos métodos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="be555-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="be555-139">Os métodos auxiliares de resultados de ação se baseiam em `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="be555-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="be555-140">Quando um tipo de modelo é retornado, o tipo de retorno é `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="be555-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="be555-141">O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="be555-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="be555-142">Por padrão, o ASP.NET Core dá suporte aos `application/json` `text/json` tipos de mídia, e `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="be555-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="be555-143">Ferramentas como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/tools) podem definir o `Accept` cabeçalho da solicitação para especificar o formato de retorno.</span><span class="sxs-lookup"><span data-stu-id="be555-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="be555-144">Quando o `Accept` cabeçalho contém um tipo ao qual o servidor dá suporte, esse tipo é retornado.</span><span class="sxs-lookup"><span data-stu-id="be555-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="be555-145">A próxima seção mostra como adicionar outros formatadores.</span><span class="sxs-lookup"><span data-stu-id="be555-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="be555-146">As ações do controlador podem retornar POCOs (objetos Plain antigos do CLR).</span><span class="sxs-lookup"><span data-stu-id="be555-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="be555-147">Quando um POCO é retornado, o tempo de execução cria automaticamente um `ObjectResult` que encapsula o objeto.</span><span class="sxs-lookup"><span data-stu-id="be555-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="be555-148">O cliente obtém o objeto serializado formatado.</span><span class="sxs-lookup"><span data-stu-id="be555-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="be555-149">Se o objeto que está sendo retornado for `null` , uma `204 No Content` resposta será retornada.</span><span class="sxs-lookup"><span data-stu-id="be555-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="be555-150">Retornando um tipo de objeto:</span><span class="sxs-lookup"><span data-stu-id="be555-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="be555-151">No código anterior, uma solicitação para um alias de autor válido retorna uma `200 OK` resposta com os dados do autor.</span><span class="sxs-lookup"><span data-stu-id="be555-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="be555-152">Uma solicitação para um alias inválido retorna uma `204 No Content` resposta.</span><span class="sxs-lookup"><span data-stu-id="be555-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="be555-153">O cabeçalho Accept</span><span class="sxs-lookup"><span data-stu-id="be555-153">The Accept header</span></span>

<span data-ttu-id="be555-154">A *negociação* de conteúdo ocorre quando um `Accept` cabeçalho é exibido na solicitação.</span><span class="sxs-lookup"><span data-stu-id="be555-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="be555-155">Quando uma solicitação contém um cabeçalho Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="be555-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="be555-156">Enumera os tipos de mídia no cabeçalho Accept na ordem de preferência.</span><span class="sxs-lookup"><span data-stu-id="be555-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="be555-157">Tenta localizar um formatador que pode produzir uma resposta em um dos formatos especificados.</span><span class="sxs-lookup"><span data-stu-id="be555-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="be555-158">Se nenhum formatador for encontrado que possa atender à solicitação do cliente, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="be555-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="be555-159">Retorna `406 Not Acceptable` se <xref:Microsoft.AspNetCore.Mvc.MvcOptions> foi definido ou-</span><span class="sxs-lookup"><span data-stu-id="be555-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="be555-160">Tenta encontrar o primeiro formatador que pode produzir uma resposta.</span><span class="sxs-lookup"><span data-stu-id="be555-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="be555-161">Se nenhum formatador estiver configurado para o formato solicitado, o primeiro formatador que pode formatar o objeto será usado.</span><span class="sxs-lookup"><span data-stu-id="be555-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="be555-162">Se nenhum `Accept` cabeçalho aparecer na solicitação:</span><span class="sxs-lookup"><span data-stu-id="be555-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="be555-163">O primeiro formatador que pode manipular o objeto é usado para serializar a resposta.</span><span class="sxs-lookup"><span data-stu-id="be555-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="be555-164">Não há nenhuma negociação ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="be555-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="be555-165">O servidor está determinando o formato a ser retornado.</span><span class="sxs-lookup"><span data-stu-id="be555-165">The server is determining what format to return.</span></span>

<span data-ttu-id="be555-166">Se o cabeçalho Accept contiver `*/*` , o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como true em <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="be555-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="be555-167">Navegadores e negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="be555-167">Browsers and content negotiation</span></span>

<span data-ttu-id="be555-168">Diferentemente dos clientes de API típicos, os navegadores da Web fornecem `Accept` cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="be555-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="be555-169">O navegador da Web especifica vários formatos, incluindo curingas.</span><span class="sxs-lookup"><span data-stu-id="be555-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="be555-170">Por padrão, quando a estrutura detecta que a solicitação é proveniente de um navegador:</span><span class="sxs-lookup"><span data-stu-id="be555-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="be555-171">O `Accept` cabeçalho é ignorado.</span><span class="sxs-lookup"><span data-stu-id="be555-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="be555-172">O conteúdo é retornado em JSON, a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="be555-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="be555-173">Isso fornece uma experiência mais consistente entre navegadores ao consumir APIs.</span><span class="sxs-lookup"><span data-stu-id="be555-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="be555-174">Para configurar um aplicativo para honrar os cabeçalhos de aceitação do navegador, defina <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> como `true` :</span><span class="sxs-lookup"><span data-stu-id="be555-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="be555-175">Configurar formatadores</span><span class="sxs-lookup"><span data-stu-id="be555-175">Configure formatters</span></span>

<span data-ttu-id="be555-176">Os aplicativos que precisam dar suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte.</span><span class="sxs-lookup"><span data-stu-id="be555-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="be555-177">Há formatadores separados para entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="be555-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="be555-178">Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="be555-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="be555-179">Os formatadores de saída são usados para formatar respostas.</span><span class="sxs-lookup"><span data-stu-id="be555-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="be555-180">Para obter informações sobre como criar um formatador personalizado, consulte [formatadores personalizados](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="be555-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="be555-181">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="be555-181">Add XML format support</span></span>

<span data-ttu-id="be555-182">Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="be555-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="be555-183">O código anterior serializa os resultados usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="be555-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="be555-184">Ao usar o código anterior, os métodos do controlador retornam o formato apropriado com base no cabeçalho da solicitação `Accept` .</span><span class="sxs-lookup"><span data-stu-id="be555-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="be555-185">Configurar formatadores com base em System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="be555-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="be555-186">Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="be555-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="be555-187">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="be555-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="be555-188">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be555-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="be555-189">Adicionar suporte ao formato JSON com base em Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="be555-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="be555-190">Antes do ASP.NET Core 3,0, os formatadores JSON usados por padrão implementaram usando o `Newtonsoft.Json` pacote.</span><span class="sxs-lookup"><span data-stu-id="be555-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="be555-191">No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="be555-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="be555-192">O suporte para `Newtonsoft.Json` formatadores e recursos baseados está disponível ao instalar o [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacote NuGet e configurá-lo no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="be555-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="be555-193">Alguns recursos podem não funcionar bem com `System.Text.Json` formatadores baseados em e exigem uma referência aos `Newtonsoft.Json` formatadores baseados em.</span><span class="sxs-lookup"><span data-stu-id="be555-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="be555-194">Continue usando os `Newtonsoft.Json` formatadores baseados em, se o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="be555-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="be555-195">Usa `Newtonsoft.Json` atributos.</span><span class="sxs-lookup"><span data-stu-id="be555-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="be555-196">Por exemplo, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="be555-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="be555-197">Personaliza as configurações de serialização.</span><span class="sxs-lookup"><span data-stu-id="be555-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="be555-198">O se baseia em recursos que o `Newtonsoft.Json` fornece.</span><span class="sxs-lookup"><span data-stu-id="be555-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="be555-199">Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="be555-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="be555-200">Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="be555-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="be555-201">Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="be555-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="be555-202">Os recursos para os `Newtonsoft.Json` formatadores baseados em podem ser configurados usando `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="be555-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="be555-203">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="be555-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="be555-204">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be555-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="be555-205">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="be555-205">Add XML format support</span></span>

<span data-ttu-id="be555-206">A formatação XML requer o pacote NuGet [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="be555-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="be555-207">Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="be555-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="be555-208">O código anterior serializa os resultados usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="be555-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="be555-209">Ao usar o código anterior, os métodos do controlador devem retornar o formato apropriado com base no `Accept` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="be555-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="be555-210">Especificar um formato</span><span class="sxs-lookup"><span data-stu-id="be555-210">Specify a format</span></span>

<span data-ttu-id="be555-211">Para restringir os formatos de resposta, aplique o [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro.</span><span class="sxs-lookup"><span data-stu-id="be555-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="be555-212">Assim como a maioria dos [filtros](xref:mvc/controllers/filters), `[Produces]` pode ser aplicado na ação, controlador ou escopo global:</span><span class="sxs-lookup"><span data-stu-id="be555-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="be555-213">O [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:</span><span class="sxs-lookup"><span data-stu-id="be555-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="be555-214">Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.</span><span class="sxs-lookup"><span data-stu-id="be555-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="be555-215">Se outros formatadores estiverem configurados e o cliente especificar um formato diferente, o JSON será retornado.</span><span class="sxs-lookup"><span data-stu-id="be555-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="be555-216">Para obter mais informações, consulte [filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="be555-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="be555-217">Formatadores de caso especiais</span><span class="sxs-lookup"><span data-stu-id="be555-217">Special case formatters</span></span>

<span data-ttu-id="be555-218">Alguns casos especiais são implementados com formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="be555-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="be555-219">Por padrão, os `string` tipos de retorno são formatados como *texto/simples* (*texto/HTML* , se solicitado por meio do `Accept` cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="be555-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="be555-220">Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="be555-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="be555-221">Os formatadores são removidos do `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="be555-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="be555-222">Ações que têm um tipo de retorno de objeto de modelo retornam `204 No Content` ao retornar `null` .</span><span class="sxs-lookup"><span data-stu-id="be555-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="be555-223">Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="be555-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="be555-224">O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="be555-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="be555-225">Sem o `StringOutputFormatter` , os formatos de formatadores JSON internos `string` retornam tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="be555-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="be555-226">Se o formatador JSON interno for removido e um formatador XML estiver disponível, o formatador XML formatará os `string` tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="be555-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="be555-227">Caso contrário, `string` retornarão tipos de retorno `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="be555-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="be555-228">Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado.</span><span class="sxs-lookup"><span data-stu-id="be555-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="be555-229">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be555-229">For example:</span></span>

* <span data-ttu-id="be555-230">O formatador JSON retorna uma resposta com um corpo de `null` .</span><span class="sxs-lookup"><span data-stu-id="be555-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="be555-231">O formatador XML retorna um elemento XML vazio com o atributo `xsi:nil="true"` definido.</span><span class="sxs-lookup"><span data-stu-id="be555-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="be555-232">Mapeamentos de URL de formato de resposta</span><span class="sxs-lookup"><span data-stu-id="be555-232">Response format URL mappings</span></span>

<span data-ttu-id="be555-233">Os clientes podem solicitar um formato específico como parte da URL, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be555-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="be555-234">Na cadeia de caracteres de consulta ou parte do caminho.</span><span class="sxs-lookup"><span data-stu-id="be555-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="be555-235">Usando uma extensão de arquivo específica de formato, como. xml ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="be555-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="be555-236">O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API.</span><span class="sxs-lookup"><span data-stu-id="be555-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="be555-237">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be555-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="be555-238">A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional.</span><span class="sxs-lookup"><span data-stu-id="be555-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="be555-239">O [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo verifica a existência do valor de formato no `RouteData` e mapeia o formato de resposta para o formatador apropriado quando a resposta é criada.</span><span class="sxs-lookup"><span data-stu-id="be555-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="be555-240">Rota</span><span class="sxs-lookup"><span data-stu-id="be555-240">Route</span></span>        |             <span data-ttu-id="be555-241">Formatador</span><span class="sxs-lookup"><span data-stu-id="be555-241">Formatter</span></span>              |
|---
<span data-ttu-id="be555-242">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-243">'Blazor'</span></span>
- <span data-ttu-id="be555-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-244">'Identity'</span></span>
- <span data-ttu-id="be555-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-246">'Razor'</span></span>
- <span data-ttu-id="be555-247">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-248">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-249">'Blazor'</span></span>
- <span data-ttu-id="be555-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-250">'Identity'</span></span>
- <span data-ttu-id="be555-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-252">'Razor'</span></span>
- <span data-ttu-id="be555-253">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-254">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-255">'Blazor'</span></span>
- <span data-ttu-id="be555-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-256">'Identity'</span></span>
- <span data-ttu-id="be555-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-258">'Razor'</span></span>
- <span data-ttu-id="be555-259">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-260">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-261">'Blazor'</span></span>
- <span data-ttu-id="be555-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-262">'Identity'</span></span>
- <span data-ttu-id="be555-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-264">'Razor'</span></span>
- <span data-ttu-id="be555-265">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-266">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-267">'Blazor'</span></span>
- <span data-ttu-id="be555-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-268">'Identity'</span></span>
- <span data-ttu-id="be555-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-270">'Razor'</span></span>
- <span data-ttu-id="be555-271">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-272">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-273">'Blazor'</span></span>
- <span data-ttu-id="be555-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-274">'Identity'</span></span>
- <span data-ttu-id="be555-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-276">'Razor'</span></span>
- <span data-ttu-id="be555-277">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-278">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-279">'Blazor'</span></span>
- <span data-ttu-id="be555-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-280">'Identity'</span></span>
- <span data-ttu-id="be555-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-282">'Razor'</span></span>
- <span data-ttu-id="be555-283">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-284">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-285">'Blazor'</span></span>
- <span data-ttu-id="be555-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-286">'Identity'</span></span>
- <span data-ttu-id="be555-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-288">'Razor'</span></span>
- <span data-ttu-id="be555-289">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-290">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-291">'Blazor'</span></span>
- <span data-ttu-id="be555-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-292">'Identity'</span></span>
- <span data-ttu-id="be555-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-294">'Razor'</span></span>
- <span data-ttu-id="be555-295">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-296">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-297">'Blazor'</span></span>
- <span data-ttu-id="be555-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-298">'Identity'</span></span>
- <span data-ttu-id="be555-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-300">'Razor'</span></span>
- <span data-ttu-id="be555-301">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-301">'SignalR' uid:</span></span> 

<span data-ttu-id="be555-302">------------|---Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-303">'Blazor'</span></span>
- <span data-ttu-id="be555-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-304">'Identity'</span></span>
- <span data-ttu-id="be555-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-306">'Razor'</span></span>
- <span data-ttu-id="be555-307">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-308">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-309">'Blazor'</span></span>
- <span data-ttu-id="be555-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-310">'Identity'</span></span>
- <span data-ttu-id="be555-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-312">'Razor'</span></span>
- <span data-ttu-id="be555-313">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-314">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-315">'Blazor'</span></span>
- <span data-ttu-id="be555-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-316">'Identity'</span></span>
- <span data-ttu-id="be555-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-318">'Razor'</span></span>
- <span data-ttu-id="be555-319">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-320">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-321">'Blazor'</span></span>
- <span data-ttu-id="be555-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-322">'Identity'</span></span>
- <span data-ttu-id="be555-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-324">'Razor'</span></span>
- <span data-ttu-id="be555-325">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-326">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-327">'Blazor'</span></span>
- <span data-ttu-id="be555-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-328">'Identity'</span></span>
- <span data-ttu-id="be555-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-330">'Razor'</span></span>
- <span data-ttu-id="be555-331">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-332">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-333">'Blazor'</span></span>
- <span data-ttu-id="be555-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-334">'Identity'</span></span>
- <span data-ttu-id="be555-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-336">'Razor'</span></span>
- <span data-ttu-id="be555-337">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-338">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-339">'Blazor'</span></span>
- <span data-ttu-id="be555-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-340">'Identity'</span></span>
- <span data-ttu-id="be555-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-342">'Razor'</span></span>
- <span data-ttu-id="be555-343">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-344">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-345">'Blazor'</span></span>
- <span data-ttu-id="be555-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-346">'Identity'</span></span>
- <span data-ttu-id="be555-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-348">'Razor'</span></span>
- <span data-ttu-id="be555-349">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-350">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-351">'Blazor'</span></span>
- <span data-ttu-id="be555-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-352">'Identity'</span></span>
- <span data-ttu-id="be555-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-354">'Razor'</span></span>
- <span data-ttu-id="be555-355">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-356">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-357">'Blazor'</span></span>
- <span data-ttu-id="be555-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-358">'Identity'</span></span>
- <span data-ttu-id="be555-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-360">'Razor'</span></span>
- <span data-ttu-id="be555-361">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-362">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-363">'Blazor'</span></span>
- <span data-ttu-id="be555-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-364">'Identity'</span></span>
- <span data-ttu-id="be555-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-366">'Razor'</span></span>
- <span data-ttu-id="be555-367">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-368">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-369">'Blazor'</span></span>
- <span data-ttu-id="be555-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-370">'Identity'</span></span>
- <span data-ttu-id="be555-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-372">'Razor'</span></span>
- <span data-ttu-id="be555-373">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-374">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-375">'Blazor'</span></span>
- <span data-ttu-id="be555-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-376">'Identity'</span></span>
- <span data-ttu-id="be555-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-378">'Razor'</span></span>
- <span data-ttu-id="be555-379">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-380">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-381">'Blazor'</span></span>
- <span data-ttu-id="be555-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-382">'Identity'</span></span>
- <span data-ttu-id="be555-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-384">'Razor'</span></span>
- <span data-ttu-id="be555-385">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-386">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-387">'Blazor'</span></span>
- <span data-ttu-id="be555-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-388">'Identity'</span></span>
- <span data-ttu-id="be555-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-390">'Razor'</span></span>
- <span data-ttu-id="be555-391">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="be555-392">Título: autor: Descrição: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="be555-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="be555-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="be555-393">'Blazor'</span></span>
- <span data-ttu-id="be555-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="be555-394">'Identity'</span></span>
- <span data-ttu-id="be555-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="be555-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="be555-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="be555-396">'Razor'</span></span>
- <span data-ttu-id="be555-397">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="be555-397">'SignalR' uid:</span></span> 

<span data-ttu-id="be555-398">------------------| |   `/api/products/5`    |    O formatador de saída padrão | | `/api/products/5.json` | O formatador JSON (se configurado) | | `/api/products/5.xml`  | O formatador XML (se configurado) |</span><span class="sxs-lookup"><span data-stu-id="be555-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
