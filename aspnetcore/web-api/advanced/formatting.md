---
<span data-ttu-id="73013-101">Título: formatar dados de resposta em ASP.NET Core autor da API Web: Descrição ardalis: saiba como formatar dados de resposta em ASP.NET Core API Web.</span><span class="sxs-lookup"><span data-stu-id="73013-101">title: Format response data in ASP.NET Core Web API author: ardalis description: Learn how to format response data in ASP.NET Core Web API.</span></span>
<span data-ttu-id="73013-102">MS. Author: Riande MS. Custom: H1Hack27Feb2017 MS. Date: 04/17/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="73013-102">ms.author: riande ms.custom: H1Hack27Feb2017 ms.date: 04/17/2020 no-loc:</span></span>
- <span data-ttu-id="73013-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="73013-103">'Blazor'</span></span>
- <span data-ttu-id="73013-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="73013-104">'Identity'</span></span>
- <span data-ttu-id="73013-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="73013-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="73013-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="73013-106">'Razor'</span></span>
- <span data-ttu-id="73013-107">SignalRUID: Web-API/Advanced/formating</span><span class="sxs-lookup"><span data-stu-id="73013-107">'SignalR' uid: web-api/advanced/formatting</span></span>

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="73013-108">Formatar dados de resposta na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73013-108">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="73013-109">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="73013-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="73013-110">ASP.NET Core MVC tem suporte para formatar dados de resposta.</span><span class="sxs-lookup"><span data-stu-id="73013-110">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="73013-111">Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado do cliente.</span><span class="sxs-lookup"><span data-stu-id="73013-111">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="73013-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73013-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="73013-113">Resultados de ação específica de formato</span><span class="sxs-lookup"><span data-stu-id="73013-113">Format-specific Action Results</span></span>

<span data-ttu-id="73013-114">Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="73013-114">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="73013-115">As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente.</span><span class="sxs-lookup"><span data-stu-id="73013-115">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="73013-116">Por exemplo, retornando `JsonResult` retorna dados formatados para JSON.</span><span class="sxs-lookup"><span data-stu-id="73013-116">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="73013-117">Retornar `ContentResult` ou uma cadeia de caracteres retorna dados de cadeia de caracteres formatados com texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="73013-117">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="73013-118">Uma ação não é necessária para retornar qualquer tipo específico.</span><span class="sxs-lookup"><span data-stu-id="73013-118">An action isn't required to return any specific type.</span></span> <span data-ttu-id="73013-119">ASP.NET Core dá suporte a qualquer valor de retorno de objeto.</span><span class="sxs-lookup"><span data-stu-id="73013-119">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="73013-120">Os resultados de ações que retornam objetos que não são <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipos são serializados usando a <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementação apropriada.</span><span class="sxs-lookup"><span data-stu-id="73013-120">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="73013-121">Para obter mais informações, consulte <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="73013-121">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="73013-122">O método auxiliar interno <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> retorna dados formatados em JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="73013-122">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="73013-123">O download de exemplo retorna a lista de autores.</span><span class="sxs-lookup"><span data-stu-id="73013-123">The sample download returns the list of authors.</span></span> <span data-ttu-id="73013-124">Usando as ferramentas de desenvolvedor de navegador F12 ou o [postmaster](https://www.getpostman.com/tools) com o código anterior:</span><span class="sxs-lookup"><span data-stu-id="73013-124">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="73013-125">O cabeçalho de resposta que contém o **tipo de conteúdo:** `application/json; charset=utf-8` é exibido.</span><span class="sxs-lookup"><span data-stu-id="73013-125">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="73013-126">Os cabeçalhos de solicitação são exibidos.</span><span class="sxs-lookup"><span data-stu-id="73013-126">The request headers are displayed.</span></span> <span data-ttu-id="73013-127">Por exemplo, o `Accept` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="73013-127">For example, the `Accept` header.</span></span> <span data-ttu-id="73013-128">O `Accept` cabeçalho é ignorado pelo código anterior.</span><span class="sxs-lookup"><span data-stu-id="73013-128">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="73013-129">Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="73013-129">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="73013-130">No código anterior, o `Content-Type` retornado é `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="73013-130">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="73013-131">O retorno de uma cadeia de caracteres é entregue `Content-Type` de `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="73013-131">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="73013-132">Para ações com vários tipos de retorno, retorne `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="73013-132">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="73013-133">Por exemplo, retornando códigos de status HTTP diferentes com base no resultado das operações executadas.</span><span class="sxs-lookup"><span data-stu-id="73013-133">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="73013-134">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="73013-134">Content negotiation</span></span>

<span data-ttu-id="73013-135">A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="73013-135">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="73013-136">O formato padrão usado pelo ASP.NET Core é [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="73013-136">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="73013-137">A negociação de conteúdo é:</span><span class="sxs-lookup"><span data-stu-id="73013-137">Content negotiation is:</span></span>

* <span data-ttu-id="73013-138">Implementado pelo <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="73013-138">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="73013-139">Incorporados aos resultados da ação específica do código de status retornados dos métodos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="73013-139">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="73013-140">Os métodos auxiliares de resultados de ação se baseiam em `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="73013-140">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="73013-141">Quando um tipo de modelo é retornado, o tipo de retorno é `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="73013-141">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="73013-142">O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="73013-142">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="73013-143">Por padrão, o ASP.NET Core dá suporte aos `application/json` `text/json` tipos de mídia, e `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="73013-143">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="73013-144">Ferramentas como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/tools) podem definir o `Accept` cabeçalho da solicitação para especificar o formato de retorno.</span><span class="sxs-lookup"><span data-stu-id="73013-144">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="73013-145">Quando o `Accept` cabeçalho contém um tipo ao qual o servidor dá suporte, esse tipo é retornado.</span><span class="sxs-lookup"><span data-stu-id="73013-145">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="73013-146">A próxima seção mostra como adicionar outros formatadores.</span><span class="sxs-lookup"><span data-stu-id="73013-146">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="73013-147">As ações do controlador podem retornar POCOs (objetos Plain antigos do CLR).</span><span class="sxs-lookup"><span data-stu-id="73013-147">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="73013-148">Quando um POCO é retornado, o tempo de execução cria automaticamente um `ObjectResult` que encapsula o objeto.</span><span class="sxs-lookup"><span data-stu-id="73013-148">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="73013-149">O cliente obtém o objeto serializado formatado.</span><span class="sxs-lookup"><span data-stu-id="73013-149">The client gets the formatted serialized object.</span></span> <span data-ttu-id="73013-150">Se o objeto que está sendo retornado for `null` , uma `204 No Content` resposta será retornada.</span><span class="sxs-lookup"><span data-stu-id="73013-150">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="73013-151">Retornando um tipo de objeto:</span><span class="sxs-lookup"><span data-stu-id="73013-151">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="73013-152">No código anterior, uma solicitação para um alias de autor válido retorna uma `200 OK` resposta com os dados do autor.</span><span class="sxs-lookup"><span data-stu-id="73013-152">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="73013-153">Uma solicitação para um alias inválido retorna uma `204 No Content` resposta.</span><span class="sxs-lookup"><span data-stu-id="73013-153">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="73013-154">O cabeçalho Accept</span><span class="sxs-lookup"><span data-stu-id="73013-154">The Accept header</span></span>

<span data-ttu-id="73013-155">A *negociação* de conteúdo ocorre quando um `Accept` cabeçalho é exibido na solicitação.</span><span class="sxs-lookup"><span data-stu-id="73013-155">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="73013-156">Quando uma solicitação contém um cabeçalho Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="73013-156">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="73013-157">Enumera os tipos de mídia no cabeçalho Accept na ordem de preferência.</span><span class="sxs-lookup"><span data-stu-id="73013-157">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="73013-158">Tenta localizar um formatador que pode produzir uma resposta em um dos formatos especificados.</span><span class="sxs-lookup"><span data-stu-id="73013-158">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="73013-159">Se nenhum formatador for encontrado que possa atender à solicitação do cliente, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="73013-159">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="73013-160">Retorna `406 Not Acceptable` se <xref:Microsoft.AspNetCore.Mvc.MvcOptions> foi definido ou-</span><span class="sxs-lookup"><span data-stu-id="73013-160">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="73013-161">Tenta encontrar o primeiro formatador que pode produzir uma resposta.</span><span class="sxs-lookup"><span data-stu-id="73013-161">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="73013-162">Se nenhum formatador estiver configurado para o formato solicitado, o primeiro formatador que pode formatar o objeto será usado.</span><span class="sxs-lookup"><span data-stu-id="73013-162">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="73013-163">Se nenhum `Accept` cabeçalho aparecer na solicitação:</span><span class="sxs-lookup"><span data-stu-id="73013-163">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="73013-164">O primeiro formatador que pode manipular o objeto é usado para serializar a resposta.</span><span class="sxs-lookup"><span data-stu-id="73013-164">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="73013-165">Não há nenhuma negociação ocorrendo.</span><span class="sxs-lookup"><span data-stu-id="73013-165">There isn't any negotiation taking place.</span></span> <span data-ttu-id="73013-166">O servidor está determinando o formato a ser retornado.</span><span class="sxs-lookup"><span data-stu-id="73013-166">The server is determining what format to return.</span></span>

<span data-ttu-id="73013-167">Se o cabeçalho Accept contiver `*/*` , o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como true em <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="73013-167">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="73013-168">Navegadores e negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="73013-168">Browsers and content negotiation</span></span>

<span data-ttu-id="73013-169">Diferentemente dos clientes de API típicos, os navegadores da Web fornecem `Accept` cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="73013-169">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="73013-170">O navegador da Web especifica vários formatos, incluindo curingas.</span><span class="sxs-lookup"><span data-stu-id="73013-170">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="73013-171">Por padrão, quando a estrutura detecta que a solicitação é proveniente de um navegador:</span><span class="sxs-lookup"><span data-stu-id="73013-171">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="73013-172">O `Accept` cabeçalho é ignorado.</span><span class="sxs-lookup"><span data-stu-id="73013-172">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="73013-173">O conteúdo é retornado em JSON, a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="73013-173">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="73013-174">Isso fornece uma experiência mais consistente entre navegadores ao consumir APIs.</span><span class="sxs-lookup"><span data-stu-id="73013-174">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="73013-175">Para configurar um aplicativo para honrar os cabeçalhos de aceitação do navegador, defina <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> como `true` :</span><span class="sxs-lookup"><span data-stu-id="73013-175">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="73013-176">Configurar formatadores</span><span class="sxs-lookup"><span data-stu-id="73013-176">Configure formatters</span></span>

<span data-ttu-id="73013-177">Os aplicativos que precisam dar suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte.</span><span class="sxs-lookup"><span data-stu-id="73013-177">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="73013-178">Há formatadores separados para entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="73013-178">There are separate formatters for input and output.</span></span> <span data-ttu-id="73013-179">Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="73013-179">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="73013-180">Os formatadores de saída são usados para formatar respostas.</span><span class="sxs-lookup"><span data-stu-id="73013-180">Output formatters are used to format responses.</span></span> <span data-ttu-id="73013-181">Para obter informações sobre como criar um formatador personalizado, consulte [formatadores personalizados](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="73013-181">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="73013-182">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="73013-182">Add XML format support</span></span>

<span data-ttu-id="73013-183">Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="73013-183">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="73013-184">O código anterior serializa os resultados usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="73013-184">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="73013-185">Ao usar o código anterior, os métodos do controlador retornam o formato apropriado com base no cabeçalho da solicitação `Accept` .</span><span class="sxs-lookup"><span data-stu-id="73013-185">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="73013-186">Configurar formatadores com base em System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="73013-186">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="73013-187">Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="73013-187">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="73013-188">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="73013-188">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="73013-189">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="73013-189">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="73013-190">Adicionar suporte ao formato JSON com base em Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="73013-190">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="73013-191">Antes do ASP.NET Core 3,0, os formatadores JSON usados por padrão implementaram usando o `Newtonsoft.Json` pacote.</span><span class="sxs-lookup"><span data-stu-id="73013-191">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="73013-192">No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="73013-192">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="73013-193">O suporte para `Newtonsoft.Json` formatadores e recursos baseados está disponível ao instalar o [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacote NuGet e configurá-lo no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73013-193">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="73013-194">Alguns recursos podem não funcionar bem com `System.Text.Json` formatadores baseados em e exigem uma referência aos `Newtonsoft.Json` formatadores baseados em.</span><span class="sxs-lookup"><span data-stu-id="73013-194">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="73013-195">Continue usando os `Newtonsoft.Json` formatadores baseados em, se o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="73013-195">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="73013-196">Usa `Newtonsoft.Json` atributos.</span><span class="sxs-lookup"><span data-stu-id="73013-196">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="73013-197">Por exemplo, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="73013-197">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="73013-198">Personaliza as configurações de serialização.</span><span class="sxs-lookup"><span data-stu-id="73013-198">Customizes the serialization settings.</span></span>
* <span data-ttu-id="73013-199">O se baseia em recursos que o `Newtonsoft.Json` fornece.</span><span class="sxs-lookup"><span data-stu-id="73013-199">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="73013-200">Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="73013-200">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="73013-201">Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="73013-201">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="73013-202">Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="73013-202">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="73013-203">Os recursos para os `Newtonsoft.Json` formatadores baseados em podem ser configurados usando `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="73013-203">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="73013-204">As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="73013-204">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="73013-205">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="73013-205">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="73013-206">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="73013-206">Add XML format support</span></span>

<span data-ttu-id="73013-207">A formatação XML requer o pacote NuGet [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="73013-207">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="73013-208">Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="73013-208">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="73013-209">O código anterior serializa os resultados usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="73013-209">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="73013-210">Ao usar o código anterior, os métodos do controlador devem retornar o formato apropriado com base no `Accept` cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="73013-210">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="73013-211">Especificar um formato</span><span class="sxs-lookup"><span data-stu-id="73013-211">Specify a format</span></span>

<span data-ttu-id="73013-212">Para restringir os formatos de resposta, aplique o [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro.</span><span class="sxs-lookup"><span data-stu-id="73013-212">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="73013-213">Assim como a maioria dos [filtros](xref:mvc/controllers/filters), `[Produces]` pode ser aplicado na ação, controlador ou escopo global:</span><span class="sxs-lookup"><span data-stu-id="73013-213">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="73013-214">O [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:</span><span class="sxs-lookup"><span data-stu-id="73013-214">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="73013-215">Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.</span><span class="sxs-lookup"><span data-stu-id="73013-215">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="73013-216">Se outros formatadores estiverem configurados e o cliente especificar um formato diferente, o JSON será retornado.</span><span class="sxs-lookup"><span data-stu-id="73013-216">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="73013-217">Para obter mais informações, consulte [filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="73013-217">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="73013-218">Formatadores de caso especiais</span><span class="sxs-lookup"><span data-stu-id="73013-218">Special case formatters</span></span>

<span data-ttu-id="73013-219">Alguns casos especiais são implementados com formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="73013-219">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="73013-220">Por padrão, os `string` tipos de retorno são formatados como *texto/simples* (*texto/HTML* , se solicitado por meio do `Accept` cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="73013-220">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="73013-221">Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="73013-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="73013-222">Os formatadores são removidos do `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="73013-222">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="73013-223">Ações que têm um tipo de retorno de objeto de modelo retornam `204 No Content` ao retornar `null` .</span><span class="sxs-lookup"><span data-stu-id="73013-223">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="73013-224">Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="73013-224">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="73013-225">O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="73013-225">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="73013-226">Sem o `StringOutputFormatter` , os formatos de formatadores JSON internos `string` retornam tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="73013-226">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="73013-227">Se o formatador JSON interno for removido e um formatador XML estiver disponível, o formatador XML formatará os `string` tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="73013-227">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="73013-228">Caso contrário, `string` retornarão tipos de retorno `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="73013-228">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="73013-229">Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado.</span><span class="sxs-lookup"><span data-stu-id="73013-229">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="73013-230">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="73013-230">For example:</span></span>

* <span data-ttu-id="73013-231">O formatador JSON retorna uma resposta com um corpo de `null` .</span><span class="sxs-lookup"><span data-stu-id="73013-231">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="73013-232">O formatador XML retorna um elemento XML vazio com o atributo `xsi:nil="true"` definido.</span><span class="sxs-lookup"><span data-stu-id="73013-232">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="73013-233">Mapeamentos de URL de formato de resposta</span><span class="sxs-lookup"><span data-stu-id="73013-233">Response format URL mappings</span></span>

<span data-ttu-id="73013-234">Os clientes podem solicitar um formato específico como parte da URL, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="73013-234">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="73013-235">Na cadeia de caracteres de consulta ou parte do caminho.</span><span class="sxs-lookup"><span data-stu-id="73013-235">In the query string or part of the path.</span></span>
* <span data-ttu-id="73013-236">Usando uma extensão de arquivo específica de formato, como. xml ou. JSON.</span><span class="sxs-lookup"><span data-stu-id="73013-236">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="73013-237">O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API.</span><span class="sxs-lookup"><span data-stu-id="73013-237">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="73013-238">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="73013-238">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="73013-239">A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional.</span><span class="sxs-lookup"><span data-stu-id="73013-239">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="73013-240">O [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo verifica a existência do valor de formato no `RouteData` e mapeia o formato de resposta para o formatador apropriado quando a resposta é criada.</span><span class="sxs-lookup"><span data-stu-id="73013-240">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="73013-241">Rota</span><span class="sxs-lookup"><span data-stu-id="73013-241">Route</span></span>        |             <span data-ttu-id="73013-242">Formatador</span><span class="sxs-lookup"><span data-stu-id="73013-242">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="73013-243">O formatador de saída padrão</span><span class="sxs-lookup"><span data-stu-id="73013-243">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="73013-244">O formatador JSON (se configurado)</span><span class="sxs-lookup"><span data-stu-id="73013-244">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="73013-245">O formatador XML (se configurado)</span><span class="sxs-lookup"><span data-stu-id="73013-245">The XML formatter (if configured)</span></span>  |
