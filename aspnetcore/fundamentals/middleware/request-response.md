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
# <a name="request-and-response-operations-in-aspnet-core"></a>Operações de solicitação e de resposta no ASP.NET Core

De [Justin Kotalik](https://github.com/jkotalik)

Este artigo explica como ler o corpo da solicitação e gravar no corpo da resposta. O código para essas operações pode ser necessário ao escrever middleware. Fora do middleware de escrita, o código personalizado geralmente não é necessário porque as operações são manipuladas pelo MVC e por Razor páginas.

Há duas abstrações para os corpos de solicitação e resposta: <xref:System.IO.Stream> e <xref:System.IO.Pipelines.Pipe> . Para a leitura da solicitação, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> é um <xref:System.IO.Stream> e `HttpRequest.BodyReader` é <xref:System.IO.Pipelines.PipeReader> um. Para a gravação de resposta, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> é um <xref:System.IO.Stream> e `HttpResponse.BodyWriter` é <xref:System.IO.Pipelines.PipeWriter> um.

Os [pipelines](/dotnet/standard/io/pipelines) são recomendados em fluxos. Os fluxos podem ser mais fáceis de usar em algumas operações simples, mas os pipelines têm uma vantagem no desempenho e são mais fáceis de usar na maioria dos cenários. ASP.NET Core está começando a usar pipelines em vez de fluxos internamente. Os exemplos incluem:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Os fluxos não estão sendo removidos da estrutura. Os fluxos continuam a ser usados em todo o .NET e muitos tipos de fluxo não têm equivalentes de pipe, como `FileStreams` e `ResponseCompression` .

## <a name="stream-examples"></a>Exemplos de fluxos

Suponha que o objetivo seja criar um middleware que leia todo o corpo da solicitação como uma lista de cadeias de caracteres, dividindo em novas linhas. Uma implementação simples de fluxo pode ter uma aparência semelhante ao exemplo a seguir:

> [!WARNING]
> O seguinte código:
> * É usado para demonstrar os problemas sem usar um pipe para ler o corpo da solicitação.
> * Não se destina a ser usado em aplicativos de produção.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Esse código funciona, mas há alguns problemas:

* Antes de ser acrescentado ao `StringBuilder`, o exemplo cria outra cadeia de caracteres (`encodedString`), que é imediatamente descartada. Esse processo ocorre em todos os bytes no fluxo, portanto, o resultado é uma alocação extra de memória do tamanho de todo o corpo da solicitação.
* O exemplo lê a cadeia de caracteres inteira antes da divisão em novas linhas. É mais eficiente verificar se há novas linhas na matriz de bytes.

Aqui está um exemplo que corrige alguns dos problemas anteriores:

> [!WARNING]
> O seguinte código:
> * É usado para demonstrar as soluções para alguns problemas no código anterior, sem resolver todos os problemas.
> * Não se destina a ser usado em aplicativos de produção.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Este exemplo anterior:

* Não armazena em buffer todo o corpo da solicitação em um `StringBuilder`, a menos que não haja caracteres da nova linha.
* Não chama `Split` na cadeia de caracteres.

No entanto, ainda há alguns problemas:

* Se os caracteres de nova linha forem esparsos, grande parte do corpo da solicitação será armazenada em buffer na cadeia de caracteres.
* O código continua a criar cadeias de `remainingString` caracteres () e as adiciona ao buffer de cadeias, o que resulta em uma alocação extra.

Esses problemas são corrigível, mas o código está se tornando cada vez mais complicado com pouca melhoria. Os pipelines oferecem uma maneira de resolver esses problemas com uma complexidade de código mínima.

## <a name="pipelines"></a>Pipelines

O exemplo a seguir mostra como o mesmo cenário pode ser manipulado usando um [PipeReader](/dotnet/standard/io/pipelines#pipe):

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Este exemplo corrige muitos problemas das implementações de fluxos:

* Não há necessidade de um buffer de cadeia de caracteres porque os `PipeReader` bytes de identificadores que não foram usados.
* As cadeias de caracteres codificadas são adicionadas diretamente à lista de cadeias de caracteres retornadas.
* Além da `ToArray` chamada, e a memória usada pela cadeia de caracteres, a criação da cadeia de caracteres é a alocação livre.

## <a name="adapters"></a>Adaptadores

As `Body` `BodyReader` Propriedades,, e `BodyWriter` estão disponíveis para o `HttpRequest` e o `HttpResponse` . Quando você define `Body` para um fluxo diferente, um novo conjunto de adaptadores adapta automaticamente cada tipo para o outro. Se você definir `HttpRequest.Body` para um novo fluxo, o `HttpRequest.BodyReader` será automaticamente definido como um novo `PipeReader` encapsulado `HttpRequest.Body` .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`é usado para indicar que os cabeçalhos são não modificáveis e para executar `OnStarting` retornos de chamada. Ao usar o Kestrel como um servidor, chamar `StartAsync` antes de usar as `PipeReader` garantias de que a memória retornada pelo `GetMemory` pertence ao Kestrel interno <xref:System.IO.Pipelines.Pipe> , e não a um buffer externo.

## <a name="additional-resources"></a>Recursos adicionais

* [System. IO. pipelines no .NET](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
