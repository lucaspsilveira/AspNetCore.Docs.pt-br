---
title: Operações de solicitação e de resposta no ASP.NET Core
author: jkotalik
description: Aprenda a ler o corpo da solicitação e grave o corpo da resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: fed9e48cdb2b33805cb05243de706b5c86853328
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84548526"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="d49f4-103">Operações de solicitação e de resposta no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d49f4-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="d49f4-104">De [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="d49f4-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d49f4-105">Este artigo explica como ler o corpo da solicitação e gravar no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="d49f4-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="d49f4-106">O código para essas operações pode ser necessário ao escrever middleware.</span><span class="sxs-lookup"><span data-stu-id="d49f4-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="d49f4-107">Fora do middleware de escrita, o código personalizado geralmente não é necessário porque as operações são manipuladas pelo MVC e por Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="d49f4-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="d49f4-108">Há duas abstrações para os corpos de solicitação e resposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe> .</span><span class="sxs-lookup"><span data-stu-id="d49f4-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="d49f4-109">Para a leitura da solicitação, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> é um <xref:System.IO.Stream> e `HttpRequest.BodyReader` é <xref:System.IO.Pipelines.PipeReader> um.</span><span class="sxs-lookup"><span data-stu-id="d49f4-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="d49f4-110">Para a gravação de resposta, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> é um <xref:System.IO.Stream> e `HttpResponse.BodyWriter` é <xref:System.IO.Pipelines.PipeWriter> um.</span><span class="sxs-lookup"><span data-stu-id="d49f4-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="d49f4-111">Os [pipelines](/dotnet/standard/io/pipelines) são recomendados em fluxos.</span><span class="sxs-lookup"><span data-stu-id="d49f4-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="d49f4-112">Os fluxos podem ser mais fáceis de usar em algumas operações simples, mas os pipelines têm uma vantagem no desempenho e são mais fáceis de usar na maioria dos cenários.</span><span class="sxs-lookup"><span data-stu-id="d49f4-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="d49f4-113">ASP.NET Core está começando a usar pipelines em vez de fluxos internamente.</span><span class="sxs-lookup"><span data-stu-id="d49f4-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="d49f4-114">Os exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="d49f4-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="d49f4-115">Os fluxos não estão sendo removidos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="d49f4-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="d49f4-116">Os fluxos continuam a ser usados em todo o .NET e muitos tipos de fluxo não têm equivalentes de pipe, como `FileStreams` e `ResponseCompression` .</span><span class="sxs-lookup"><span data-stu-id="d49f4-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="d49f4-117">Exemplos de fluxos</span><span class="sxs-lookup"><span data-stu-id="d49f4-117">Stream examples</span></span>

<span data-ttu-id="d49f4-118">Suponha que o objetivo seja criar um middleware que leia todo o corpo da solicitação como uma lista de cadeias de caracteres, dividindo em novas linhas.</span><span class="sxs-lookup"><span data-stu-id="d49f4-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="d49f4-119">Uma implementação simples de fluxo pode ter uma aparência semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d49f4-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="d49f4-120">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d49f4-120">The following code:</span></span>
> * <span data-ttu-id="d49f4-121">É usado para demonstrar os problemas sem usar um pipe para ler o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d49f4-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="d49f4-122">Não se destina a ser usado em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="d49f4-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d49f4-123">Esse código funciona, mas há alguns problemas:</span><span class="sxs-lookup"><span data-stu-id="d49f4-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="d49f4-124">Antes de ser acrescentado ao `StringBuilder`, o exemplo cria outra cadeia de caracteres (`encodedString`), que é imediatamente descartada.</span><span class="sxs-lookup"><span data-stu-id="d49f4-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="d49f4-125">Esse processo ocorre em todos os bytes no fluxo, portanto, o resultado é uma alocação extra de memória do tamanho de todo o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d49f4-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="d49f4-126">O exemplo lê a cadeia de caracteres inteira antes da divisão em novas linhas.</span><span class="sxs-lookup"><span data-stu-id="d49f4-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="d49f4-127">É mais eficiente verificar se há novas linhas na matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="d49f4-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="d49f4-128">Aqui está um exemplo que corrige alguns dos problemas anteriores:</span><span class="sxs-lookup"><span data-stu-id="d49f4-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="d49f4-129">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d49f4-129">The following code:</span></span>
> * <span data-ttu-id="d49f4-130">É usado para demonstrar as soluções para alguns problemas no código anterior, sem resolver todos os problemas.</span><span class="sxs-lookup"><span data-stu-id="d49f4-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="d49f4-131">Não se destina a ser usado em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="d49f4-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="d49f4-132">Este exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="d49f4-132">This preceding example:</span></span>

* <span data-ttu-id="d49f4-133">Não armazena em buffer todo o corpo da solicitação em um `StringBuilder`, a menos que não haja caracteres da nova linha.</span><span class="sxs-lookup"><span data-stu-id="d49f4-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="d49f4-134">Não chama `Split` na cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="d49f4-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="d49f4-135">No entanto, ainda há alguns problemas:</span><span class="sxs-lookup"><span data-stu-id="d49f4-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="d49f4-136">Se os caracteres de nova linha forem esparsos, grande parte do corpo da solicitação será armazenada em buffer na cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="d49f4-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="d49f4-137">O código continua a criar cadeias de `remainingString` caracteres () e as adiciona ao buffer de cadeias, o que resulta em uma alocação extra.</span><span class="sxs-lookup"><span data-stu-id="d49f4-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="d49f4-138">Esses problemas são corrigível, mas o código está se tornando cada vez mais complicado com pouca melhoria.</span><span class="sxs-lookup"><span data-stu-id="d49f4-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="d49f4-139">Os pipelines oferecem uma maneira de resolver esses problemas com uma complexidade de código mínima.</span><span class="sxs-lookup"><span data-stu-id="d49f4-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="d49f4-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="d49f4-140">Pipelines</span></span>

<span data-ttu-id="d49f4-141">O exemplo a seguir mostra como o mesmo cenário pode ser manipulado usando um [PipeReader](/dotnet/standard/io/pipelines#pipe):</span><span class="sxs-lookup"><span data-stu-id="d49f4-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="d49f4-142">Este exemplo corrige muitos problemas das implementações de fluxos:</span><span class="sxs-lookup"><span data-stu-id="d49f4-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="d49f4-143">Não há necessidade de um buffer de cadeia de caracteres porque os `PipeReader` bytes de identificadores que não foram usados.</span><span class="sxs-lookup"><span data-stu-id="d49f4-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="d49f4-144">As cadeias de caracteres codificadas são adicionadas diretamente à lista de cadeias de caracteres retornadas.</span><span class="sxs-lookup"><span data-stu-id="d49f4-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="d49f4-145">Além da `ToArray` chamada, e a memória usada pela cadeia de caracteres, a criação da cadeia de caracteres é a alocação livre.</span><span class="sxs-lookup"><span data-stu-id="d49f4-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="d49f4-146">Adaptadores</span><span class="sxs-lookup"><span data-stu-id="d49f4-146">Adapters</span></span>

<span data-ttu-id="d49f4-147">As `Body` `BodyReader` Propriedades,, e `BodyWriter` estão disponíveis para o `HttpRequest` e o `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="d49f4-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="d49f4-148">Quando você define `Body` para um fluxo diferente, um novo conjunto de adaptadores adapta automaticamente cada tipo para o outro.</span><span class="sxs-lookup"><span data-stu-id="d49f4-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="d49f4-149">Se você definir `HttpRequest.Body` para um novo fluxo, o `HttpRequest.BodyReader` será automaticamente definido como um novo `PipeReader` encapsulado `HttpRequest.Body` .</span><span class="sxs-lookup"><span data-stu-id="d49f4-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="d49f4-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d49f4-150">StartAsync</span></span>

<span data-ttu-id="d49f4-151">`HttpResponse.StartAsync`é usado para indicar que os cabeçalhos são não modificáveis e para executar `OnStarting` retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="d49f4-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="d49f4-152">Ao usar o Kestrel como um servidor, chamar `StartAsync` antes de usar as `PipeReader` garantias de que a memória retornada pelo `GetMemory` pertence ao Kestrel interno <xref:System.IO.Pipelines.Pipe> , e não a um buffer externo.</span><span class="sxs-lookup"><span data-stu-id="d49f4-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d49f4-153">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d49f4-153">Additional resources</span></span>

* [<span data-ttu-id="d49f4-154">System. IO. pipelines no .NET</span><span class="sxs-lookup"><span data-stu-id="d49f4-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
