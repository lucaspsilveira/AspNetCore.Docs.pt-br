---
title: Formatar dados de resposta na API Web ASP.NET Core
author: ardalis
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642706"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="99344-103">Formatar dados de resposta na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99344-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="99344-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="99344-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="99344-105">ASP.NET O Core MVC tem suporte para formatação de dados de resposta.</span><span class="sxs-lookup"><span data-stu-id="99344-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="99344-106">Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="99344-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="99344-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99344-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="99344-108">Resultados de ação específicos para formato</span><span class="sxs-lookup"><span data-stu-id="99344-108">Format-specific Action Results</span></span>

<span data-ttu-id="99344-109">Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="99344-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="99344-110">As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente.</span><span class="sxs-lookup"><span data-stu-id="99344-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="99344-111">Por exemplo, `JsonResult` o retorno de dados formatados por JSON.</span><span class="sxs-lookup"><span data-stu-id="99344-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="99344-112">O `ContentResult` retorno ou uma seqüência retorna dados de seqüência formatados em texto simples.</span><span class="sxs-lookup"><span data-stu-id="99344-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="99344-113">Uma ação não é necessária para retornar qualquer tipo específico.</span><span class="sxs-lookup"><span data-stu-id="99344-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="99344-114">ASP.NET Core suporta qualquer valor de retorno de objeto.</span><span class="sxs-lookup"><span data-stu-id="99344-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="99344-115">Os resultados de ações que <xref:Microsoft.AspNetCore.Mvc.IActionResult> retornam objetos que <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> não são tipos são serializados usando a implementação apropriada.</span><span class="sxs-lookup"><span data-stu-id="99344-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="99344-116">Para obter mais informações, consulte <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="99344-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="99344-117">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> de ajuda incorporado retorna dados formatados por JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="99344-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="99344-118">O download da amostra retorna a lista de autores.</span><span class="sxs-lookup"><span data-stu-id="99344-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="99344-119">Usando as ferramentas de desenvolvedor do navegador F12 ou [Carteiro](https://www.getpostman.com/tools) com o código anterior:</span><span class="sxs-lookup"><span data-stu-id="99344-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="99344-120">O cabeçalho de resposta que contém **o tipo de conteúdo:** `application/json; charset=utf-8` é exibido.</span><span class="sxs-lookup"><span data-stu-id="99344-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="99344-121">Os cabeçalhos de solicitação são exibidos.</span><span class="sxs-lookup"><span data-stu-id="99344-121">The request headers are displayed.</span></span> <span data-ttu-id="99344-122">Por exemplo, `Accept` o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="99344-122">For example, the `Accept` header.</span></span> <span data-ttu-id="99344-123">O `Accept` cabeçalho é ignorado pelo código anterior.</span><span class="sxs-lookup"><span data-stu-id="99344-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="99344-124">Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="99344-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="99344-125">No código anterior, `Content-Type` o `text/plain`retornado é .</span><span class="sxs-lookup"><span data-stu-id="99344-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="99344-126">Retornando uma seqüência `Content-Type` de entregas de: `text/plain`</span><span class="sxs-lookup"><span data-stu-id="99344-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="99344-127">Para ações com vários `IActionResult`tipos de retorno, retorne .</span><span class="sxs-lookup"><span data-stu-id="99344-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="99344-128">Por exemplo, retornando diferentes códigos de status HTTP com base no resultado das operações realizadas.</span><span class="sxs-lookup"><span data-stu-id="99344-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="99344-129">Negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="99344-129">Content negotiation</span></span>

<span data-ttu-id="99344-130">A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Aceitar](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="99344-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="99344-131">O formato padrão usado por ASP.NET Core é [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="99344-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="99344-132">Negociação de conteúdo é:</span><span class="sxs-lookup"><span data-stu-id="99344-132">Content negotiation is:</span></span>

* <span data-ttu-id="99344-133">Implementado <xref:Microsoft.AspNetCore.Mvc.ObjectResult>por .</span><span class="sxs-lookup"><span data-stu-id="99344-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="99344-134">Incorporada aos resultados de ação específicos do código de status retornados dos métodos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="99344-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="99344-135">Os métodos auxiliares dos `ObjectResult`resultados de ação são baseados em .</span><span class="sxs-lookup"><span data-stu-id="99344-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="99344-136">Quando um tipo de modelo é `ObjectResult`retornado, o tipo de retorno é .</span><span class="sxs-lookup"><span data-stu-id="99344-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="99344-137">O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="99344-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="99344-138">Por padrão, ASP.NET `application/json`Core `text/json`suporta `text/plain` , e tipos de mídia.</span><span class="sxs-lookup"><span data-stu-id="99344-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="99344-139">Ferramentas como [Fiddler](https://www.telerik.com/fiddler) ou [Carteiro](https://www.getpostman.com/tools) `Accept` podem definir o cabeçalho de solicitação para especificar o formato de retorno.</span><span class="sxs-lookup"><span data-stu-id="99344-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="99344-140">Quando `Accept` o cabeçalho contém um tipo que o servidor suporta, esse tipo é retornado.</span><span class="sxs-lookup"><span data-stu-id="99344-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="99344-141">A próxima seção mostra como adicionar matérias adicionais.</span><span class="sxs-lookup"><span data-stu-id="99344-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="99344-142">As ações do controlador podem retornar POCOs (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="99344-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="99344-143">Quando um POCO é devolvido, o tempo `ObjectResult` de execução cria automaticamente um que envolve o objeto.</span><span class="sxs-lookup"><span data-stu-id="99344-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="99344-144">O cliente recebe o objeto serializado formatado.</span><span class="sxs-lookup"><span data-stu-id="99344-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="99344-145">Se o objeto que `null`está `204 No Content` sendo devolvido é, uma resposta é devolvida.</span><span class="sxs-lookup"><span data-stu-id="99344-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="99344-146">Retornando um tipo de objeto:</span><span class="sxs-lookup"><span data-stu-id="99344-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="99344-147">No código anterior, uma solicitação de um `200 OK` alias de autor válido retorna uma resposta com os dados do autor.</span><span class="sxs-lookup"><span data-stu-id="99344-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="99344-148">Uma solicitação de um alias `204 No Content` inválido retorna uma resposta.</span><span class="sxs-lookup"><span data-stu-id="99344-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="99344-149">O cabeçalho Accept</span><span class="sxs-lookup"><span data-stu-id="99344-149">The Accept header</span></span>

<span data-ttu-id="99344-150">A *negociação* de `Accept` conteúdo ocorre quando um cabeçalho aparece na solicitação.</span><span class="sxs-lookup"><span data-stu-id="99344-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="99344-151">Quando uma solicitação contém um cabeçalho de aceitação, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="99344-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="99344-152">Enumera os tipos de mídia no cabeçalho de aceitação na ordem de preferência.</span><span class="sxs-lookup"><span data-stu-id="99344-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="99344-153">Tenta encontrar um formatter que possa produzir uma resposta em um dos formatos especificados.</span><span class="sxs-lookup"><span data-stu-id="99344-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="99344-154">Se não for encontrado, não for encontrado, isso possa satisfazer a solicitação do cliente, ASP.NET Núcleo:</span><span class="sxs-lookup"><span data-stu-id="99344-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="99344-155">Retorna `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> se tiver sido definido, ou -</span><span class="sxs-lookup"><span data-stu-id="99344-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="99344-156">Tenta encontrar o primeiro formatter que pode produzir uma resposta.</span><span class="sxs-lookup"><span data-stu-id="99344-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="99344-157">Se não for configurado para o formato solicitado, o primeiro formatter que pode formatar o objeto é usado.</span><span class="sxs-lookup"><span data-stu-id="99344-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="99344-158">Se `Accept` nenhum cabeçalho aparecer na solicitação:</span><span class="sxs-lookup"><span data-stu-id="99344-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="99344-159">O primeiro assunto que pode lidar com o objeto é usado para serializar a resposta.</span><span class="sxs-lookup"><span data-stu-id="99344-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="99344-160">Não há nenhuma negociação acontecendo.</span><span class="sxs-lookup"><span data-stu-id="99344-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="99344-161">O servidor está determinando qual formato retornar.</span><span class="sxs-lookup"><span data-stu-id="99344-161">The server is determining what format to return.</span></span>

<span data-ttu-id="99344-162">Se o cabeçalho Aceitar contiver, `*/*`o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como verdadeiro . <xref:Microsoft.AspNetCore.Mvc.MvcOptions></span><span class="sxs-lookup"><span data-stu-id="99344-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="99344-163">Navegadores e negociação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="99344-163">Browsers and content negotiation</span></span>

<span data-ttu-id="99344-164">Ao contrário dos clientes típicos da API, os navegadores da Web fornecem `Accept` cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="99344-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="99344-165">O navegador da Web especifica muitos formatos, incluindo curingas.</span><span class="sxs-lookup"><span data-stu-id="99344-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="99344-166">Por padrão, quando a estrutura detectar que a solicitação está vindo de um navegador:</span><span class="sxs-lookup"><span data-stu-id="99344-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="99344-167">O `Accept` cabeçalho é ignorado.</span><span class="sxs-lookup"><span data-stu-id="99344-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="99344-168">O conteúdo é devolvido em JSON, a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="99344-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="99344-169">Isso fornece uma experiência mais consistente entre os navegadores ao consumir APIs.</span><span class="sxs-lookup"><span data-stu-id="99344-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="99344-170">Para configurar um aplicativo para honrar os <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`cabeçalhos de aceitação do navegador, defina:</span><span class="sxs-lookup"><span data-stu-id="99344-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="99344-171">Configurar matérias-de-matérias</span><span class="sxs-lookup"><span data-stu-id="99344-171">Configure formatters</span></span>

<span data-ttu-id="99344-172">Aplicativos que precisam de suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte.</span><span class="sxs-lookup"><span data-stu-id="99344-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="99344-173">Há formatadores separados para entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="99344-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="99344-174">Os assuntos de entrada são usados pelo [Model Binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="99344-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="99344-175">Os assuntos de saída são usados para formatar respostas.</span><span class="sxs-lookup"><span data-stu-id="99344-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="99344-176">Para obter informações sobre a criação de um formatter personalizado, consulte [Formatters personalizados](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="99344-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="99344-177">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="99344-177">Add XML format support</span></span>

<span data-ttu-id="99344-178">Os assuntos de <xref:System.Xml.Serialization.XmlSerializer> foro xml <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>implementados usando são configurados por chamada:</span><span class="sxs-lookup"><span data-stu-id="99344-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="99344-179">O código anterior serializa `XmlSerializer`os resultados usando .</span><span class="sxs-lookup"><span data-stu-id="99344-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="99344-180">Ao usar o código anterior, os métodos do controlador `Accept` retornam o formato apropriado com base no cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="99344-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="99344-181">Configurar formatadores com base em System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="99344-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="99344-182">Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="99344-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="99344-183">As opções de serialização de saída, por `JsonResult`ação, podem ser configuradas usando .</span><span class="sxs-lookup"><span data-stu-id="99344-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="99344-184">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="99344-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="99344-185">Adicionar suporte ao formato JSON com base em Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="99344-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="99344-186">Antes de ASP.NET O Core 3.0, o padrão `Newtonsoft.Json` usado JSON formatters implementado usando o pacote.</span><span class="sxs-lookup"><span data-stu-id="99344-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="99344-187">No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="99344-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="99344-188">O `Newtonsoft.Json` suporte para assuntos e recursos baseados está disponível instalando o pacote [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet e configurando-o em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="99344-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="99344-189">Alguns recursos podem `System.Text.Json`não funcionar bem com assuntos `Newtonsoft.Json`forobaseados e requerem uma referência aos assuntos formatters baseados em -.</span><span class="sxs-lookup"><span data-stu-id="99344-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="99344-190">Continue usando `Newtonsoft.Json`os formatters baseados em formatters se o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="99344-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="99344-191">Usa `Newtonsoft.Json` atributos.</span><span class="sxs-lookup"><span data-stu-id="99344-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="99344-192">Por exemplo, `[JsonProperty]` ou `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="99344-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="99344-193">Personaliza as configurações de serialização.</span><span class="sxs-lookup"><span data-stu-id="99344-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="99344-194">Conta com `Newtonsoft.Json` recursos que fornecem.</span><span class="sxs-lookup"><span data-stu-id="99344-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="99344-195">Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="99344-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="99344-196">Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="99344-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="99344-197">Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="99344-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="99344-198">Os recursos `Newtonsoft.Json`para assuntos foretais baseados podem ser configurados usando: `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`</span><span class="sxs-lookup"><span data-stu-id="99344-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="99344-199">As opções de serialização de saída, por `JsonResult`ação, podem ser configuradas usando .</span><span class="sxs-lookup"><span data-stu-id="99344-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="99344-200">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="99344-200">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="99344-201">Adicionar suporte ao formato XML</span><span class="sxs-lookup"><span data-stu-id="99344-201">Add XML format support</span></span>

<span data-ttu-id="99344-202">A formatação XML requer o pacote [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="99344-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="99344-203">Os assuntos de <xref:System.Xml.Serialization.XmlSerializer> foro xml <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>implementados usando são configurados por chamada:</span><span class="sxs-lookup"><span data-stu-id="99344-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="99344-204">O código anterior serializa `XmlSerializer`os resultados usando .</span><span class="sxs-lookup"><span data-stu-id="99344-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="99344-205">Ao usar o código anterior, os métodos do controlador `Accept` devem retornar o formato apropriado com base no cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="99344-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="99344-206">Especificar um formato</span><span class="sxs-lookup"><span data-stu-id="99344-206">Specify a format</span></span>

<span data-ttu-id="99344-207">Para restringir os formatos [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) de resposta, aplique o filtro.</span><span class="sxs-lookup"><span data-stu-id="99344-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="99344-208">Como a maioria `[Produces]` [dos filtros,](xref:mvc/controllers/filters)pode ser aplicado no escopo de ação, controlador ou global:</span><span class="sxs-lookup"><span data-stu-id="99344-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="99344-209">O [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:</span><span class="sxs-lookup"><span data-stu-id="99344-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="99344-210">Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.</span><span class="sxs-lookup"><span data-stu-id="99344-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="99344-211">Se outros assuntos forem configurados e o cliente especificar um formato diferente, o JSON será devolvido.</span><span class="sxs-lookup"><span data-stu-id="99344-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="99344-212">Para obter mais informações, consulte [Filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="99344-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="99344-213">Caso especial para assuntos</span><span class="sxs-lookup"><span data-stu-id="99344-213">Special case formatters</span></span>

<span data-ttu-id="99344-214">Alguns casos especiais são implementados com formatadores internos.</span><span class="sxs-lookup"><span data-stu-id="99344-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="99344-215">Por `string` padrão, os tipos de devolução são formatados como `Accept` *texto/simples* *(texto/html,* se solicitado através do cabeçalho).</span><span class="sxs-lookup"><span data-stu-id="99344-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="99344-216">Esse comportamento pode ser excluído <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>removendo o .</span><span class="sxs-lookup"><span data-stu-id="99344-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="99344-217">Os assuntos formatters `ConfigureServices` são removidos no método.</span><span class="sxs-lookup"><span data-stu-id="99344-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="99344-218">Ações que possuem um tipo `204 No Content` de `null`retorno do objeto modelo retornam ao retornar .</span><span class="sxs-lookup"><span data-stu-id="99344-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="99344-219">Esse comportamento pode ser excluído <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>removendo o .</span><span class="sxs-lookup"><span data-stu-id="99344-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="99344-220">O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="99344-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="99344-221">Sem `StringOutputFormatter`os formatos de retorno de `string` matéria-entrada JSON incorporados.</span><span class="sxs-lookup"><span data-stu-id="99344-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="99344-222">Se a matéria-matéria JSON incorporada for removida e uma matéria xml estiver `string` disponível, os formatos de matéria-por-matéria XML são tipos de retorno.</span><span class="sxs-lookup"><span data-stu-id="99344-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="99344-223">Caso contrário, `string` os `406 Not Acceptable`tipos de retorno retornam .</span><span class="sxs-lookup"><span data-stu-id="99344-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="99344-224">Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado.</span><span class="sxs-lookup"><span data-stu-id="99344-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="99344-225">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="99344-225">For example:</span></span>

* <span data-ttu-id="99344-226">O assunto json retorna uma resposta `null`com um corpo de .</span><span class="sxs-lookup"><span data-stu-id="99344-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="99344-227">O formatter XML retorna um elemento `xsi:nil="true"` XML vazio com o conjunto de atributos.</span><span class="sxs-lookup"><span data-stu-id="99344-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="99344-228">Mapeamentos de URL de formato de resposta</span><span class="sxs-lookup"><span data-stu-id="99344-228">Response format URL mappings</span></span>

<span data-ttu-id="99344-229">Os clientes podem solicitar um formato específico como parte da URL, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="99344-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="99344-230">Na seqüência de consulta ou parte do caminho.</span><span class="sxs-lookup"><span data-stu-id="99344-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="99344-231">Usando uma extensão de arquivo específica para formato, como .xml ou .json.</span><span class="sxs-lookup"><span data-stu-id="99344-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="99344-232">O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API.</span><span class="sxs-lookup"><span data-stu-id="99344-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="99344-233">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="99344-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="99344-234">A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional.</span><span class="sxs-lookup"><span data-stu-id="99344-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="99344-235">O [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo verifica a existência do `RouteData` valor do formato no e mapeia o formato de resposta para o formatter apropriado quando a resposta é criada.</span><span class="sxs-lookup"><span data-stu-id="99344-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="99344-236">Rota</span><span class="sxs-lookup"><span data-stu-id="99344-236">Route</span></span>        |             <span data-ttu-id="99344-237">Formatador</span><span class="sxs-lookup"><span data-stu-id="99344-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="99344-238">O formatador de saída padrão</span><span class="sxs-lookup"><span data-stu-id="99344-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="99344-239">O formatador JSON (se configurado)</span><span class="sxs-lookup"><span data-stu-id="99344-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="99344-240">O formatador XML (se configurado)</span><span class="sxs-lookup"><span data-stu-id="99344-240">The XML formatter (if configured)</span></span>  |
