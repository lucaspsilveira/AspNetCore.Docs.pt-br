---
title: Gerenciamento de memória e padrões em ASP.NET Core
author: rick-anderson
description: Saiba como a memória é gerenciada em ASP.NET Core e como funciona o coletor de lixo (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440942"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Gestão de memória e coleta de lixo (GC) no Núcleo ASP.NET

Por [Sébastien Ros](https://github.com/sebastienros) e [Rick Anderson](https://twitter.com/RickAndMSFT)

O gerenciamento de memória é complexo, mesmo em uma estrutura gerenciada como .NET. Analisar e entender problemas de memória pode ser um desafio. Este artigo:

* Foi motivado por muitos *vazamentos de memória* e *GC não problemas de trabalho.* A maioria desses problemas foi causada por não entender como o consumo de memória funciona no .NET Core, ou não entender como ele é medido.
* Demonstra uso problemático da memória e sugere abordagens alternativas.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Como funciona a coleta de lixo (GC) no .NET Core

O CG aloca segmentos de pilha onde cada segmento é uma faixa de memória contígua. Os objetos colocados no monte são categorizados em uma das 3 gerações: 0, 1 ou 2. A geração determina a frequência que o GC tenta liberar memória em objetos gerenciados que não são mais referenciados pelo aplicativo. As gerações numeradas mais baixas são gc'd com mais freqüência.

Objetos são movidos de uma geração para outra com base em sua vida. À medida que os objetos vivem mais, eles são movidos para uma geração superior. Como mencionado anteriormente, as gerações mais altas são GC'd menos freqüentemente. Objetos vividos a curto prazo sempre permanecem na geração 0. Por exemplo, objetos que são referenciados durante a vida de uma solicitação web têm vida curta. Singletons de nível [de](xref:fundamentals/dependency-injection#service-lifetimes) aplicação geralmente migram para a geração 2.

Quando um aplicativo ASP.NET Core é iniciado, o GC:

* Reserva alguma memória para os segmentos iniciais de pilha.
* Compromete uma pequena parte da memória quando o tempo de execução é carregado.

As alocações de memória anteriores são feitas por razões de desempenho. O benefício de desempenho vem de segmentos de pilha em memória contígua.

### <a name="call-gccollect"></a>Ligue para a GC. Coletar

Chamando [gc. Coletar](xref:System.GC.Collect*) explicitamente:

* **Não** deve ser feito pela produção ASP.NET aplicativos Core.
* É útil ao investigar vazamentos de memória.
* Ao investigar, verifica-se que o CG removeu todos os objetos pendurados da memória para que a memória possa ser medida.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analisando o uso da memória de um aplicativo

Ferramentas dedicadas podem ajudar a analisar o uso da memória:

- Contagem de referências de objeto
- Medindo quanto impacto o GC tem no uso da CPU
- Medição do espaço de memória usado para cada geração

Use as seguintes ferramentas para analisar o uso da memória:

* [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Pode ser usado em máquinas de produção.
* [Analisar o uso da memória sem o depurador visual studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Uso de memória de perfil no Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Detectando problemas de memória

O Gerenciador de Tarefas pode ser usado para ter uma ideia de quanta memória um aplicativo de ASP.NET está usando. O valor de memória do Gerenciador de tarefas:

* Representa a quantidade de memória que é usada pelo processo de ASP.NET.
* Inclui objetos vivos do aplicativo e outros consumidores de memória, como o uso de memória nativa.

Se o valor de memória do Gerenciador de Tarefas aumentar indefinidamente e nunca se achatar, o aplicativo tem um vazamento de memória. As seções a seguir demonstram e explicam vários padrões de uso da memória.

## <a name="sample-display-memory-usage-app"></a>Aplicativo de uso de memória de exibição de amostra

O [aplicativo de exemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponível no GitHub. O aplicativo MemoryLeak:

* Inclui um controlador de diagnóstico que coleta memória em tempo real e dados GC para o aplicativo.
* Tem uma página De índice que exibe a memória e os dados gc. A página Índice é atualizada a cada segundo.
* Contém um controlador de API que fornece vários padrões de carga de memória.
* Não é uma ferramenta suportada, no entanto, ela pode ser usada para exibir padrões de uso de memória de aplicativos ASP.NET Core.

Executar MemoryLeak. A memória alocada aumenta lentamente até que ocorra um GC. A memória aumenta porque a ferramenta aloca objeto personalizado para capturar dados. A imagem a seguir mostra a página Índice de vazamento de memória quando ocorre um Gen 0 GC. O gráfico mostra 0 RPS (Solicitações por segundo) porque nenhum ponto final da API do controlador de API foi chamado.

![gráfico anterior](memory/_static/0RPS.png)

O gráfico exibe dois valores para o uso da memória:

- Alocado: a quantidade de memória ocupada por objetos gerenciados
- [Conjunto de trabalho](/windows/win32/memory/working-set): O conjunto de páginas no espaço de endereço virtual do processo que atualmente residem na memória física. O conjunto de trabalho mostrado é o mesmo valor exibido pelo Gerenciador de Tarefas.

### <a name="transient-objects"></a>Objetos transitórios

A API a seguir cria uma instância de string de 10 KB e a devolve ao cliente. Em cada solicitação, um novo objeto é alocado na memória e escrito para a resposta. As strings são armazenadas como caracteres UTF-16 em .NET, de modo que cada caractere leva 2 bytes na memória.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

O gráfico a seguir é gerado com uma carga relativamente pequena para mostrar como as alocações de memória são impactadas pelo GC.

![gráfico anterior](memory/_static/bigstring.png)

O gráfico anterior mostra:

* 4K RPS (Solicitações por segundo).
* As coleções da Geração 0 GC ocorrem a cada dois segundos.
* O conjunto de trabalho é constante em aproximadamente 500 MB.
* Cpu é de 12%.
* O consumo de memória e liberação (através de GC) é estável.

O gráfico a seguir é feito no throughput máximo que pode ser manuseado pela máquina.

![gráfico anterior](memory/_static/bigstring2.png)

O gráfico anterior mostra:

* RPS 22K
* As coleções de geração 0 GC ocorrem várias vezes por segundo.
* As coleções da Geração 1 são acionadas porque o aplicativo alocou significativamente mais memória por segundo.
* O conjunto de trabalho é constante em aproximadamente 500 MB.
* A CPU é de 33%.
* O consumo de memória e liberação (através de GC) é estável.
* A CPU (33%) não é superutilizada, portanto, a coleta de lixo pode acompanhar um alto número de alocações.

### <a name="workstation-gc-vs-server-gc"></a>Estação de Trabalho GC vs. Servidor GC

O Coletor de Lixo .NET tem dois modos diferentes:

* **Estação de Trabalho GC**: Otimizado para o desktop.
* **Servidor GC**. O GC padrão para aplicativos ASP.NET Core. Otimizado para o servidor.

O modo GC pode ser definido explicitamente no arquivo do projeto ou no arquivo *runtimeconfig.json* do aplicativo publicado. A marcação a `ServerGarbageCollection` seguir mostra a configuração no arquivo do projeto:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

A `ServerGarbageCollection` alteração no arquivo do projeto requer que o aplicativo seja reconstruído.

**Nota:** A coleta de lixo do servidor **não** está disponível em máquinas com um único núcleo. Para obter mais informações, consulte <xref:System.Runtime.GCSettings.IsServerGC>.

A imagem a seguir mostra o perfil de memória sob um RPS 5K usando a Estação de Trabalho GC.

![gráfico anterior](memory/_static/workstation.png)

As diferenças entre este gráfico e a versão do servidor são significativas:

- O conjunto de trabalho cai de 500 MB para 70 MB.
- O GC faz coletas de geração 0 várias vezes por segundo em vez de a cada dois segundos.
- Gc cai de 300 MB para 10 MB.

Em um ambiente típico de servidor web, o uso da CPU é mais importante do que a memória, portanto o Server GC é melhor. Se a utilização da memória for alta e o uso da CPU for relativamente baixo, o Workstation GC pode ser mais performático. Por exemplo, alta densidade hospedando vários aplicativos web onde a memória é escassa.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC usando Docker e pequenos recipientes

Quando vários aplicativos em contêiner estão sendo executados em uma máquina, o Workstation GC pode ser mais preformant do que o Server GC. Para obter mais informações, consulte [Executar com o Servidor GC em um pequeno contêiner](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) e executar com o Servidor GC em um cenário de pequeno contêiner Parte 1 – Limite rígido para o Heap [GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Referências persistentes de objeto

O GC não pode liberar objetos que são referenciados. Objetos que são referenciados, mas não são mais necessários, resultam em um vazamento de memória. Se o aplicativo alocar objetos com freqüência e não os liberar depois que eles não forem mais necessários, o uso da memória aumentará com o tempo.

A API a seguir cria uma instância de string de 10 KB e a devolve ao cliente. A diferença com o exemplo anterior é que esta instância é referenciada por um membro estático, o que significa que nunca está disponível para coleta.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

O código anterior:

* É um exemplo de um típico vazamento de memória.
* Com chamadas freqüentes, faz com que a `OutOfMemory` memória do aplicativo aumente até que o processo se desfaça com uma exceção.

![gráfico anterior](memory/_static/eternal.png)

Na imagem anterior:

* O teste `/api/staticstring` de carga do ponto final causa um aumento linear da memória.
* O GC tenta libertar a memória à medida que a pressão da memória cresce, chamando uma coleção de geração 2.
* O GC não pode liberar a memória vazada. Alocado e conjunto de trabalho aumentam com o tempo.

Alguns cenários, como o cache, exigem que as referências dos objetos sejam mantidas até que a pressão da memória os obrigue a serem liberados. A <xref:System.WeakReference> classe pode ser usada para este tipo de código de cache. Um `WeakReference` objeto é coletado sob pressões de memória. A implementação <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`padrão de usos .

### <a name="native-memory"></a>Memória nativa

Alguns objetos .NET Core dependem da memória nativa. A memória nativa **não** pode ser coletada pelo GC. O objeto .NET usando memória nativa deve libertá-lo usando código nativo.

.NET fornece <xref:System.IDisposable> a interface para permitir que os desenvolvedores liberem a memória nativa. Mesmo <xref:System.IDisposable.Dispose*> que não seja chamado, `Dispose` as classes corretamente implementadas chamam quando o [finalizador](/dotnet/csharp/programming-guide/classes-and-structs/destructors) é executado.

Considere o código a seguir:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) é uma classe gerenciada, portanto, qualquer instância será coletada no final da solicitação.

A imagem a seguir mostra o `fileprovider` perfil de memória enquanto invoca a API continuamente.

![gráfico anterior](memory/_static/fileprovider.png)

O gráfico anterior mostra um problema óbvio com a implementação desta classe, pois continua aumentando o uso da memória. Este é um problema conhecido que está sendo rastreado [nesta questão.](https://github.com/dotnet/aspnetcore/issues/3110)

O mesmo vazamento pode acontecer no código do usuário, por um dos seguintes:

* Não liberando a aula corretamente.
* Esquecendo-se `Dispose`de invocar o método dos objetos dependentes que devem ser eliminados.

### <a name="large-objects-heap"></a>Grandes objetos amontoam

A alocação de memória frequente/ciclos livres podem fragmentar a memória, especialmente ao alocar grandes pedaços de memória. Os objetos são alocados em blocos contíguos de memória. Para mitigar a fragmentação, quando o GC libera a memória, ele tenta desfragmentá-la. Esse processo é chamado **de compactação.** A compactação envolve a movimentação de objetos. Mover objetos grandes impõe uma penalidade de desempenho. Por esta razão, o GC cria uma zona de memória especial para objetos _grandes,_ chamada [heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Objetos maiores que 85.000 bytes (aproximadamente 83 KB) são:

* Colocado no LOH.
* Não compactado.
* Coletadodurante a geração 2 GCs.

Quando o LOH estiver cheio, o GC acionará uma coleção de geração 2. Coleções da Geração 2:

* São inerentemente lentos.
* Além disso, incorrer no custo de desencadear uma coleção em todas as outras gerações.

O código a seguir compacta o LOH imediatamente:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Consulte <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> informações sobre a compactação do LOH.

Em contêineres usando .NET Core 3.0 e posterior, o LOH é automaticamente compactado.

A seguinte API que ilustra esse comportamento:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

O gráfico a seguir mostra `/api/loh/84975` o perfil de memória de chamar o ponto final, sob carga máxima:

![gráfico anterior](memory/_static/loh1.png)

O gráfico a seguir mostra `/api/loh/84976` o perfil de memória de chamar o ponto final, alocando *apenas mais um byte*:

![gráfico anterior](memory/_static/loh2.png)

Nota: `byte[]` A estrutura tem bytes aéreos. É por isso que 84.976 bytes acionam o limite de 85.000.

Comparando os dois gráficos anteriores:

* O conjunto de trabalho é semelhante para ambos os cenários, cerca de 450 MB.
* As solicitações de LOH (84.975 bytes) mostram principalmente coleções de geração 0.
* As solicitações over LOH geram coleções constantes de geração 2. As coleções da geração 2 são caras. Mais CPU é necessária e o throughput cai quase 50%.

Objetos grandes temporários são particularmente problemáticos porque causam gcs gen2.

Para o máximo desempenho, o uso de objetos grandes deve ser minimizado. Se possível, separe objetos grandes. Por exemplo, o middleware [Response Caching](xref:performance/caching/response) no ASP.NET Core dividiu as entradas de cache em blocos inferiores a 85.000 bytes.

Os links a seguir mostram a abordagem ASP.NET Core para manter objetos abaixo do limite LOH:

* [ResponseCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Para obter mais informações, consulte:

* [Grande pilha de objetos descoberto](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Grande pilha de objetos](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

O uso <xref:System.Net.Http.HttpClient> incorreto pode resultar em um vazamento de recursos. Recursos do sistema, como conexões de banco de dados, soquetes, cabos de arquivos, etc.:

* São mais escassos do que a memória.
* São mais problemáticos quando vazadas do que a memória.

Desenvolvedores experientes .NET <xref:System.IDisposable.Dispose*> sabem chamar <xref:System.IDisposable>objetos que implementam . Não descartar objetos `IDisposable` que implementam normalmente resulta em memória vazada ou recursos do sistema vazados.

`HttpClient`implementos `IDisposable`, mas **não** devem ser descartados em cada invocação. Em `HttpClient` vez disso, deve ser reutilizado.

O ponto final a seguir `HttpClient` cria e elimina uma nova instância em cada solicitação:

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Em carga, as seguintes mensagens de erro são registradas:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Mesmo que `HttpClient` as instâncias sejam descartadas, a conexão de rede real leva algum tempo para ser liberada pelo sistema operacional. Ao criar continuamente novas conexões, ocorre _a exaustão das portas._ Cada conexão com o cliente requer sua própria porta de cliente.

Uma maneira de evitar a exaustão `HttpClient` da porta é reutilizar a mesma instância:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

A `HttpClient` instância é liberada quando o aplicativo pára. Este exemplo mostra que nem todos os recursos descartáveis devem ser eliminados após cada uso.

Veja a seguir uma melhor maneira de `HttpClient` lidar com a vida útil de uma instância:

* [HttpClient e gerenciamento de tempo de vida](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [BLOG DE FÁBRICA HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Agrupamento de objetos

O exemplo anterior `HttpClient` mostrou como a instância pode ser feita estática e reutilizada por todas as solicitações. A reutilização evita ficar sem recursos.

Agrupamento de objetos:

* Usa o padrão de reutilização.
* É projetado para objetos que são caros de criar.

Uma piscina é uma coleção de objetos pré-inicializados que podem ser reservados e liberados através de threads. Os pools podem definir regras de alocação, como limites, tamanhos predefinidos ou taxa de crescimento.

O pacote NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contém classes que ajudam a gerenciar tais pools.

O ponto final da API `byte` a seguir instancia um buffer preenchido com números aleatórios em cada solicitação:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

O gráfico a seguir chama a API anterior com carga moderada:

![gráfico anterior](memory/_static/array.png)

No gráfico anterior, as coleções da geração 0 acontecem aproximadamente uma vez por segundo.

O código anterior pode ser otimizado agrupando o `byte` buffer usando [arrayPool\<T>](xref:System.Buffers.ArrayPool`1). Uma instância estática é reutilizada entre as solicitações.

O que é diferente com essa abordagem é que um objeto agrupado é devolvido da API. Isso significa:

* O objeto está fora de seu controle assim que você voltar do método.
* Você não pode liberar o objeto.

Para configurar a eliminação do objeto:

* Encapsular a matriz agrupada em um objeto descartável.
* Registre o objeto pooled com [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose`cuidará de `Dispose`chamar o objeto de destino para que ele só seja liberado quando a solicitação HTTP estiver concluída.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

Aplicar a mesma carga que a versão não agrupada resulta no gráfico a seguir:

![gráfico anterior](memory/_static/pooledarray.png)

A principal diferença é a alocação de bytes, e como conseqüência muito menos coleções de geração 0.

## <a name="additional-resources"></a>Recursos adicionais

* [Coleta de lixo](/dotnet/standard/garbage-collection/)
* [Entendendo diferentes modos gc com o Simultcurrency Visualizer](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Grande pilha de objetos descoberto](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Grande pilha de objetos](/dotnet/standard/garbage-collection/large-object-heap)
