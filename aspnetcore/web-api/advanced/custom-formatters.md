---
title: Formatadores personalizados na API Web ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar formatadores personalizados para APIs Web no ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 27819f77cf86c946ab0415d3583dfbab80a24cf5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408858"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="176f7-103">Formatadores personalizados na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="176f7-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="176f7-104">Por [Kirk Larkin](https://twitter.com/serpent5) e [Tom Dykstra](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="176f7-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="176f7-105">ASP.NET Core MVC dá suporte à troca de dados em APIs Web usando formatadores de entrada e saída.</span><span class="sxs-lookup"><span data-stu-id="176f7-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="176f7-106">Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="176f7-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="176f7-107">Os formatadores de saída são usados para [Formatar respostas](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="176f7-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="176f7-108">A estrutura fornece formatadores de entrada e saída internos para JSON e XML.</span><span class="sxs-lookup"><span data-stu-id="176f7-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="176f7-109">Ele fornece um formatador de saída interno para texto sem formatação, mas não fornece um formatador de entrada para texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="176f7-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="176f7-110">Este artigo mostra como adicionar suporte para formatos adicionais criando formatadores personalizados.</span><span class="sxs-lookup"><span data-stu-id="176f7-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="176f7-111">Para obter um exemplo de um formatador de entrada de texto sem formatação personalizado, consulte [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) no github.</span><span class="sxs-lookup"><span data-stu-id="176f7-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="176f7-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="176f7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="176f7-113">Quando usar formatadores personalizados</span><span class="sxs-lookup"><span data-stu-id="176f7-113">When to use custom formatters</span></span>

<span data-ttu-id="176f7-114">Use um formatador personalizado para adicionar suporte a um tipo de conteúdo que não é tratado pelos formatadores Bult.</span><span class="sxs-lookup"><span data-stu-id="176f7-114">Use a custom formatter to add support for a content type that isn't handled by the bult-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="176f7-115">Visão geral de como usar um formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="176f7-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="176f7-116">Para criar um formatador personalizado:</span><span class="sxs-lookup"><span data-stu-id="176f7-116">To create a custom formatter:</span></span>

* <span data-ttu-id="176f7-117">Para serializar dados enviados ao cliente, crie uma classe de formatador de saída.</span><span class="sxs-lookup"><span data-stu-id="176f7-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="176f7-118">Para dados de deserialzing recebidos do cliente, crie uma classe de formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="176f7-118">For deserialzing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="176f7-119">Adicione instâncias de classes de formatador `InputFormatters` às `OutputFormatters` coleções e em [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="176f7-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="176f7-120">Como criar uma classe de formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="176f7-120">How to create a custom formatter class</span></span>

<span data-ttu-id="176f7-121">Para criar um formatador:</span><span class="sxs-lookup"><span data-stu-id="176f7-121">To create a formatter:</span></span>

* <span data-ttu-id="176f7-122">Derive a classe da classe base apropriada.</span><span class="sxs-lookup"><span data-stu-id="176f7-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="176f7-123">O aplicativo de exemplo deriva de <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> e <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="176f7-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="176f7-124">Especifique codificações e tipos de mídia válidos no construtor.</span><span class="sxs-lookup"><span data-stu-id="176f7-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="176f7-125">Substitua os métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> e <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.</span><span class="sxs-lookup"><span data-stu-id="176f7-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="176f7-126">Substitua os métodos <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> e `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="176f7-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="176f7-127">O código a seguir mostra a `VcardOutputFormatter` classe do [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="176f7-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="176f7-128">Derivar da classe base apropriada</span><span class="sxs-lookup"><span data-stu-id="176f7-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="176f7-129">Para tipos de mídia de texto (por exemplo, vCard), derive da classe base [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="176f7-129">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="176f7-130">Para tipos binários, derive da classe base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="176f7-130">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="176f7-131">Especifique codificações e tipos de mídia válidos</span><span class="sxs-lookup"><span data-stu-id="176f7-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="176f7-132">No construtor, especifique codificações e tipos de mídia válidos adicionando-os às coleções `SupportedMediaTypes` e `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="176f7-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

<span data-ttu-id="176f7-133">Uma classe de formatador **não** pode usar injeção de construtor para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="176f7-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="176f7-134">Por exemplo, `ILogger<VcardOutputFormatter>` não pode ser adicionado como um parâmetro ao construtor.</span><span class="sxs-lookup"><span data-stu-id="176f7-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="176f7-135">Para acessar os serviços, use o objeto de contexto que é passado para os métodos.</span><span class="sxs-lookup"><span data-stu-id="176f7-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="176f7-136">Um exemplo de código neste artigo e o [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) mostram como fazer isso.</span><span class="sxs-lookup"><span data-stu-id="176f7-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="176f7-137">Substituir canreadtype e canwritetype</span><span class="sxs-lookup"><span data-stu-id="176f7-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="176f7-138">Especifique o tipo para desserializar em ou serializar de substituindo `CanReadType` os `CanWriteType` métodos ou.</span><span class="sxs-lookup"><span data-stu-id="176f7-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="176f7-139">Por exemplo, a criação de texto de vCard a partir de um `Contact` tipo e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="176f7-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="176f7-140">O método CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="176f7-140">The CanWriteResult method</span></span>

<span data-ttu-id="176f7-141">Em alguns cenários, `CanWriteResult` deve ser substituído em vez de `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="176f7-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="176f7-142">Use `CanWriteResult` se as condições a seguir forem verdadeiras:</span><span class="sxs-lookup"><span data-stu-id="176f7-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="176f7-143">O método Action retorna uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="176f7-143">The action method returns a model class.</span></span>
* <span data-ttu-id="176f7-144">Há classes derivadas que podem ser retornadas em runtime.</span><span class="sxs-lookup"><span data-stu-id="176f7-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="176f7-145">A classe derivada retornada pela ação deve ser conhecida em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="176f7-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="176f7-146">Por exemplo, suponha que o método de ação:</span><span class="sxs-lookup"><span data-stu-id="176f7-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="176f7-147">A assinatura retorna um `Person` tipo.</span><span class="sxs-lookup"><span data-stu-id="176f7-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="176f7-148">Pode retornar um `Student` `Instructor` tipo ou derivado de `Person` .</span><span class="sxs-lookup"><span data-stu-id="176f7-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="176f7-149">Para que o formatador manipule somente `Student` objetos, verifique o tipo de [objeto](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) no objeto de contexto fornecido para o `CanWriteResult` método.</span><span class="sxs-lookup"><span data-stu-id="176f7-149">For the formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="176f7-150">Quando o método de ação retorna `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="176f7-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="176f7-151">Não é necessário usar `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="176f7-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="176f7-152">O `CanWriteType` método recebe o tipo de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="176f7-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="176f7-153">Substituir ReadRequestBodyAsync e WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="176f7-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="176f7-154">A desserialização ou a serialização é executada no `ReadRequestBodyAsync` ou no `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="176f7-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="176f7-155">O exemplo a seguir mostra como obter serviços do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="176f7-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="176f7-156">Os serviços não podem ser obtidos dos parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="176f7-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="176f7-157">Como configurar o MVC para usar um formatador personalizado</span><span class="sxs-lookup"><span data-stu-id="176f7-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="176f7-158">Para usar um formatador personalizado, adicione uma instância da classe de formatador à coleção `InputFormatters` ou `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="176f7-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="176f7-159">Formatadores são avaliados na ordem em que você os insere.</span><span class="sxs-lookup"><span data-stu-id="176f7-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="176f7-160">O primeiro deles tem precedência.</span><span class="sxs-lookup"><span data-stu-id="176f7-160">The first one takes precedence.</span></span>

## <a name="the-completed-vcardinputformatter-class"></a><span data-ttu-id="176f7-161">A `VcardInputFormatter` classe concluída</span><span class="sxs-lookup"><span data-stu-id="176f7-161">The completed `VcardInputFormatter` class</span></span>

<span data-ttu-id="176f7-162">O código a seguir mostra a `VcardInputFormatter` classe do [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="176f7-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a><span data-ttu-id="176f7-163">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="176f7-163">Test the app</span></span>

<span data-ttu-id="176f7-164">[Execute o aplicativo de exemplo deste artigo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), que implementa os formatadores de entrada e saída do vCard básico.</span><span class="sxs-lookup"><span data-stu-id="176f7-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="176f7-165">O aplicativo lê e grava vCards semelhantes ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="176f7-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="176f7-166">Para ver a saída do vCard, execute o aplicativo e envie uma solicitação get com o cabeçalho Accept `text/vcard` para `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="176f7-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="176f7-167">Para adicionar um vCard à coleção de contatos na memória:</span><span class="sxs-lookup"><span data-stu-id="176f7-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="176f7-168">Envie uma `Post` solicitação para `/api/contacts` com uma ferramenta como o postmaster.</span><span class="sxs-lookup"><span data-stu-id="176f7-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="176f7-169">Defina o cabeçalho `Content-Type` como `text/vcard`.</span><span class="sxs-lookup"><span data-stu-id="176f7-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="176f7-170">Defina o `vCard` texto no corpo, formatado como o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="176f7-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="176f7-171">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="176f7-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
