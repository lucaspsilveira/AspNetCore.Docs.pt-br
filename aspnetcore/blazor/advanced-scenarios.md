---
title: ASP.NET Blazor Core cenários avançados
author: guardrex
description: Saiba mais sobre Blazorcenários avançados em , incluindo como incorporar a lógica manual do RenderTreeBuilder em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659449"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>cenários avançados de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Manipulador de circuito blazor server

O Blazor Server permite que o código defina um *manipulador de circuitos,* que permite executar código em alterações no estado do circuito de um usuário. Um manipulador de circuitos `CircuitHandler` é implementado por derivação e registro da classe no contêiner de serviço do aplicativo. O exemplo a seguir de um manipulador de circuitos rastreia conexões signalR abertas:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

Os manipuladores de circuito são registrados usando DI. As instâncias escopo são criadas por instância de um circuito. Usando `TrackingCircuitHandler` o exemplo anterior, um serviço singleton é criado porque o estado de todos os circuitos deve ser rastreado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Se os métodos de um manipulador de circuito personalizado lançar uma exceção não manuseada, a exceção é fatal para o circuito Blazor Server. Para tolerar exceções no código de um manipulador ou nos métodos chamados, enrole o código em uma ou mais declarações [de tentativa de captura](/dotnet/csharp/language-reference/keywords/try-catch) com manipulação e registro de erros.

Quando um circuito termina porque um usuário desligou e a estrutura está limpando o estado do circuito, a estrutura elimina o escopo DI do circuito. A eliminação do escopo elimina todos os serviços DI com escopo de circuito que implementam <xref:System.IDisposable?displayProperty=fullName>. Se qualquer serviço DI lançar uma exceção não manuseada durante a eliminação, o framework registrará a exceção.

## <a name="manual-rendertreebuilder-logic"></a>Lógica manual do RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fornece métodos para manipular componentes e elementos, incluindo componentes de construção manualmente em código C#.

> [!NOTE]
> O `RenderTreeBuilder` uso para criar componentes é um cenário avançado. Um componente malformado (por exemplo, uma tag de marcação não fechada) pode resultar em comportamento indefinido.

Considere o `PetDetails` seguinte componente, que pode ser construído manualmente em outro componente:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

No exemplo a seguir, `CreateComponent` o loop `PetDetails` no método gera três componentes. Ao `RenderTreeBuilder` chamar métodos para criar`OpenComponent` `AddAttribute`os componentes (e), os números de seqüência são números de linha de código fonte. O algoritmo de diferença de Blazor baseia-se nos números de seqüência correspondentes a linhas distintas de código, não invocações de chamadas distintas. Ao criar um `RenderTreeBuilder` componente com métodos, codifique os argumentos para números de seqüência. **Usar um cálculo ou contador para gerar o número de seqüência pode levar a um desempenho ruim.** Para obter mais informações, consulte os [números de seqüência relacionados com números de linha de código e não](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) seção de ordem de execução.

`BuiltContent`Componente:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Os tipos `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* das operações de renderização. Estes são detalhes internos da implementação do quadro blazor. Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Os números de seqüência se relacionam com números de linha de código e não com ordem de execução

Os arquivos de componentes da*navalha (.razor)* são sempre compilados. A compilação é uma vantagem potencial sobre a interpretação do código porque a etapa de compilação pode ser usada para injetar informações que melhorem o desempenho do aplicativo em tempo de execução.

Um exemplo-chave dessas melhorias envolve *números de seqüência*. Os números de seqüência indicam para o tempo de execução quais saídas vieram das quais linhas de código distintas e ordenadas. O tempo de execução usa essas informações para gerar difusões de árvores eficientes em tempo linear, o que é muito mais rápido do que é normalmente possível para um algoritmo geral de difusão de árvores.

Considere o seguinte componente de navalha *(.razor)* arquivo:

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

O código anterior compila algo como:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Quando o código é executado pela `someFlag` `true`primeira vez, se for, o construtor recebe:

| Sequência | Type      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | Primeiro  |
| 1        | Nó de texto | Segundo |

Imagine `someFlag` que `false`isso se torne , e a marcação é renderizada novamente. Desta vez, o construtor recebe:

| Sequência | Type       | Dados   |
| :------: | ---------- | :----: |
| 1        | Nó de texto  | Segundo |

Quando o tempo de execução executa um diff, ele vê que o item na seqüência `0` foi removido, de modo que gera o seguinte script de *edição*trivial:

* Remova o primeiro nó de texto.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>O problema de gerar números de seqüência programática

Imagine, em vez disso, que você escreveu a seguinte lógica de construtor de árvores de renderização:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Agora, a primeira saída é:

| Sequência | Type      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | Primeiro  |
| 1        | Nó de texto | Segundo |

Este resultado é idêntico ao caso anterior, portanto não existem problemas negativos. `someFlag`está `false` na segunda renderização, e a saída é:

| Sequência | Type      | Dados   |
| :------: | --------- | ------ |
| 0        | Nó de texto | Segundo |

Desta vez, o algoritmo diff vê que *duas* alterações ocorreram, e o algoritmo gera o seguinte script de edição:

* Alterar o valor do primeiro `Second`nó de texto para .
* Remova o segundo nó de texto.

A geração dos números de seqüência `if/else` perdeu todas as informações úteis sobre onde os ramos e loops estavam presentes no código original. Isso resulta em um diff **duas vezes maior do que** antes.

Este é um exemplo trivial. Em casos mais realistas, com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho geralmente é maior. Em vez de identificar imediatamente quais blocos de loop ou galhos foram inseridos ou removidos, o algoritmo diff tem que reamaldiçoar profundamente nas árvores de renderização. Isso geralmente resulta em ter que construir scripts de edição mais longos porque o algoritmo diff está mal informado sobre como as estruturas antigas e novas se relacionam entre si.

### <a name="guidance-and-conclusions"></a>Orientação e conclusões

* O desempenho do aplicativo sofre se os números de seqüência forem gerados dinamicamente.
* A estrutura não pode criar seus próprios números de seqüência automaticamente em tempo de execução porque as informações necessárias não existem a menos que sejam capturadas no momento da compilação.
* Não escreva longos blocos de `RenderTreeBuilder` lógica implementada manualmente. Prefira arquivos *.razor* e permita que o compilador lide com os números de seqüência. Se você não conseguir `RenderTreeBuilder` evitar a lógica manual, divida longos blocos de código em pedaços menores envoltos em `OpenRegion` / `CloseRegion` chamadas. Cada região tem seu próprio espaço separado de números de seqüência, para que você possa reiniciar a partir de zero (ou qualquer outro número arbitrário) dentro de cada região.
* Se os números de seqüência forem codificados, o algoritmo diff só requer que os números de seqüência aumentem de valor. O valor inicial e as lacunas são irrelevantes. Uma opção legítima é usar o número da linha de código como número de seqüência, ou começar a partir de zero e aumentar por uns ou centenas (ou qualquer intervalo preferido). 
* Blazorusa números de seqüência, enquanto outras estruturas de iu difusora de árvores não as usam. A difusão é muito mais rápida Blazor quando os números de seqüência são usados, e tem a vantagem de uma etapa de compilação que lida com números de seqüência automaticamente para desenvolvedores que compõem arquivos *.razor.*

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Realizar grandes transferências Blazor de dados em aplicativos do Servidor

Em alguns cenários, grandes quantidades de Blazordados devem ser transferidas entre JavaScript e . Normalmente, grandes transferências de dados ocorrem quando:

* ApIs do sistema de arquivos do navegador são usadas para carregar ou baixar um arquivo.
* Interop com uma biblioteca de terceiros é necessário.

No Blazor Servidor, existe uma limitação para evitar a passagem de mensagens grandes únicas que podem resultar em problemas de desempenho.

Considere a seguinte orientação ao desenvolver código Blazorque transfere dados entre JavaScript e :

* Corte os dados em pedaços menores e envie os segmentos de dados sequencialmente até que todos os dados sejam recebidos pelo servidor.
* Não aloque objetos grandes no código JavaScript e C#.
* Não bloqueie o segmento principal de iu por longos períodos ao enviar ou receber dados.
* Liberte qualquer memória consumida quando o processo estiver concluído ou cancelado.
* Imponha os seguintes requisitos adicionais para fins de segurança:
  * Declare o arquivo máximo ou tamanho de dados que podem ser passados.
  * Declare a taxa mínima de upload do cliente para o servidor.
* Depois que os dados são recebidos pelo servidor, os dados podem ser:
  * Armazenado temporariamente em um buffer de memória até que todos os segmentos sejam coletados.
  * Consumido imediatamente. Por exemplo, os dados podem ser armazenados imediatamente em um banco de dados ou gravados em disco à medida que cada segmento é recebido.

A classe de uploader de arquivo a seguir lida com js interop com o cliente. A classe uploader usa o interop JS para:

* Pesquise o cliente para enviar um segmento de dados.
* Aborte a transação se os horários de votação forem cancelados.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

No exemplo anterior:

* O `_maxBase64SegmentSize` é `8192`definido para, `_maxBase64SegmentSize = _segmentSize * 4 / 3`que é calculado a partir de .
* As APIs de gerenciamento de memória .NET Core de baixo `_uploadedSegments`nível são usadas para armazenar os segmentos de memória no servidor em .
* Um `ReceiveFile` método é usado para lidar com o upload através do interop JS:
  * O tamanho do arquivo é determinado em bytes através de JS interop com `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.
  * O número de segmentos a `numberOfSegments`serem recebidos é calculado e armazenado em .
  * Os segmentos são `for` solicitados em um `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`loop através de JS interop com . Todos os segmentos, menos o último, devem ser 8.192 bytes antes da decodificação. O cliente é forçado a enviar os dados de forma eficiente.
  * Para cada segmento recebido, as verificações são realizadas antes da decodificação com <xref:System.Convert.TryFromBase64String*>.
  * Um fluxo com os dados é <xref:System.IO.Stream> `SegmentedStream`retornado como um novo ( ) após o upload ser concluído.

A classe de fluxo segmentado expõe a lista de <xref:System.IO.Stream>segmentos como um não-buscado não-procurado:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

O código a seguir implementa funções JavaScript para receber os dados:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
