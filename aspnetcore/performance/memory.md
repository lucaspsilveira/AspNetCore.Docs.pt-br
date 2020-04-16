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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="6eb51-103">Gestão de memória e coleta de lixo (GC) no Núcleo ASP.NET</span><span class="sxs-lookup"><span data-stu-id="6eb51-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="6eb51-104">Por [Sébastien Ros](https://github.com/sebastienros) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6eb51-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6eb51-105">O gerenciamento de memória é complexo, mesmo em uma estrutura gerenciada como .NET.</span><span class="sxs-lookup"><span data-stu-id="6eb51-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="6eb51-106">Analisar e entender problemas de memória pode ser um desafio.</span><span class="sxs-lookup"><span data-stu-id="6eb51-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="6eb51-107">Este artigo:</span><span class="sxs-lookup"><span data-stu-id="6eb51-107">This article:</span></span>

* <span data-ttu-id="6eb51-108">Foi motivado por muitos *vazamentos de memória* e *GC não problemas de trabalho.*</span><span class="sxs-lookup"><span data-stu-id="6eb51-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="6eb51-109">A maioria desses problemas foi causada por não entender como o consumo de memória funciona no .NET Core, ou não entender como ele é medido.</span><span class="sxs-lookup"><span data-stu-id="6eb51-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="6eb51-110">Demonstra uso problemático da memória e sugere abordagens alternativas.</span><span class="sxs-lookup"><span data-stu-id="6eb51-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="6eb51-111">Como funciona a coleta de lixo (GC) no .NET Core</span><span class="sxs-lookup"><span data-stu-id="6eb51-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="6eb51-112">O CG aloca segmentos de pilha onde cada segmento é uma faixa de memória contígua.</span><span class="sxs-lookup"><span data-stu-id="6eb51-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="6eb51-113">Os objetos colocados no monte são categorizados em uma das 3 gerações: 0, 1 ou 2.</span><span class="sxs-lookup"><span data-stu-id="6eb51-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="6eb51-114">A geração determina a frequência que o GC tenta liberar memória em objetos gerenciados que não são mais referenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="6eb51-115">As gerações numeradas mais baixas são gc'd com mais freqüência.</span><span class="sxs-lookup"><span data-stu-id="6eb51-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="6eb51-116">Objetos são movidos de uma geração para outra com base em sua vida.</span><span class="sxs-lookup"><span data-stu-id="6eb51-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="6eb51-117">À medida que os objetos vivem mais, eles são movidos para uma geração superior.</span><span class="sxs-lookup"><span data-stu-id="6eb51-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="6eb51-118">Como mencionado anteriormente, as gerações mais altas são GC'd menos freqüentemente.</span><span class="sxs-lookup"><span data-stu-id="6eb51-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="6eb51-119">Objetos vividos a curto prazo sempre permanecem na geração 0.</span><span class="sxs-lookup"><span data-stu-id="6eb51-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="6eb51-120">Por exemplo, objetos que são referenciados durante a vida de uma solicitação web têm vida curta.</span><span class="sxs-lookup"><span data-stu-id="6eb51-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="6eb51-121">Singletons de nível [de](xref:fundamentals/dependency-injection#service-lifetimes) aplicação geralmente migram para a geração 2.</span><span class="sxs-lookup"><span data-stu-id="6eb51-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="6eb51-122">Quando um aplicativo ASP.NET Core é iniciado, o GC:</span><span class="sxs-lookup"><span data-stu-id="6eb51-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="6eb51-123">Reserva alguma memória para os segmentos iniciais de pilha.</span><span class="sxs-lookup"><span data-stu-id="6eb51-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="6eb51-124">Compromete uma pequena parte da memória quando o tempo de execução é carregado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="6eb51-125">As alocações de memória anteriores são feitas por razões de desempenho.</span><span class="sxs-lookup"><span data-stu-id="6eb51-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="6eb51-126">O benefício de desempenho vem de segmentos de pilha em memória contígua.</span><span class="sxs-lookup"><span data-stu-id="6eb51-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="6eb51-127">Ligue para a GC. Coletar</span><span class="sxs-lookup"><span data-stu-id="6eb51-127">Call GC.Collect</span></span>

<span data-ttu-id="6eb51-128">Chamando [gc. Coletar](xref:System.GC.Collect*) explicitamente:</span><span class="sxs-lookup"><span data-stu-id="6eb51-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="6eb51-129">**Não** deve ser feito pela produção ASP.NET aplicativos Core.</span><span class="sxs-lookup"><span data-stu-id="6eb51-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="6eb51-130">É útil ao investigar vazamentos de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="6eb51-131">Ao investigar, verifica-se que o CG removeu todos os objetos pendurados da memória para que a memória possa ser medida.</span><span class="sxs-lookup"><span data-stu-id="6eb51-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="6eb51-132">Analisando o uso da memória de um aplicativo</span><span class="sxs-lookup"><span data-stu-id="6eb51-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="6eb51-133">Ferramentas dedicadas podem ajudar a analisar o uso da memória:</span><span class="sxs-lookup"><span data-stu-id="6eb51-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="6eb51-134">Contagem de referências de objeto</span><span class="sxs-lookup"><span data-stu-id="6eb51-134">Counting object references</span></span>
- <span data-ttu-id="6eb51-135">Medindo quanto impacto o GC tem no uso da CPU</span><span class="sxs-lookup"><span data-stu-id="6eb51-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="6eb51-136">Medição do espaço de memória usado para cada geração</span><span class="sxs-lookup"><span data-stu-id="6eb51-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="6eb51-137">Use as seguintes ferramentas para analisar o uso da memória:</span><span class="sxs-lookup"><span data-stu-id="6eb51-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="6eb51-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Pode ser usado em máquinas de produção.</span><span class="sxs-lookup"><span data-stu-id="6eb51-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="6eb51-139">Analisar o uso da memória sem o depurador visual studio</span><span class="sxs-lookup"><span data-stu-id="6eb51-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="6eb51-140">Uso de memória de perfil no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6eb51-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="6eb51-141">Detectando problemas de memória</span><span class="sxs-lookup"><span data-stu-id="6eb51-141">Detecting memory issues</span></span>

<span data-ttu-id="6eb51-142">O Gerenciador de Tarefas pode ser usado para ter uma ideia de quanta memória um aplicativo de ASP.NET está usando.</span><span class="sxs-lookup"><span data-stu-id="6eb51-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="6eb51-143">O valor de memória do Gerenciador de tarefas:</span><span class="sxs-lookup"><span data-stu-id="6eb51-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="6eb51-144">Representa a quantidade de memória que é usada pelo processo de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6eb51-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="6eb51-145">Inclui objetos vivos do aplicativo e outros consumidores de memória, como o uso de memória nativa.</span><span class="sxs-lookup"><span data-stu-id="6eb51-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="6eb51-146">Se o valor de memória do Gerenciador de Tarefas aumentar indefinidamente e nunca se achatar, o aplicativo tem um vazamento de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="6eb51-147">As seções a seguir demonstram e explicam vários padrões de uso da memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="6eb51-148">Aplicativo de uso de memória de exibição de amostra</span><span class="sxs-lookup"><span data-stu-id="6eb51-148">Sample display memory usage app</span></span>

<span data-ttu-id="6eb51-149">O [aplicativo de exemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponível no GitHub.</span><span class="sxs-lookup"><span data-stu-id="6eb51-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="6eb51-150">O aplicativo MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="6eb51-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="6eb51-151">Inclui um controlador de diagnóstico que coleta memória em tempo real e dados GC para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="6eb51-152">Tem uma página De índice que exibe a memória e os dados gc.</span><span class="sxs-lookup"><span data-stu-id="6eb51-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="6eb51-153">A página Índice é atualizada a cada segundo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="6eb51-154">Contém um controlador de API que fornece vários padrões de carga de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="6eb51-155">Não é uma ferramenta suportada, no entanto, ela pode ser usada para exibir padrões de uso de memória de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6eb51-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="6eb51-156">Executar MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="6eb51-156">Run MemoryLeak.</span></span> <span data-ttu-id="6eb51-157">A memória alocada aumenta lentamente até que ocorra um GC.</span><span class="sxs-lookup"><span data-stu-id="6eb51-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="6eb51-158">A memória aumenta porque a ferramenta aloca objeto personalizado para capturar dados.</span><span class="sxs-lookup"><span data-stu-id="6eb51-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="6eb51-159">A imagem a seguir mostra a página Índice de vazamento de memória quando ocorre um Gen 0 GC.</span><span class="sxs-lookup"><span data-stu-id="6eb51-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="6eb51-160">O gráfico mostra 0 RPS (Solicitações por segundo) porque nenhum ponto final da API do controlador de API foi chamado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![gráfico anterior](memory/_static/0RPS.png)

<span data-ttu-id="6eb51-162">O gráfico exibe dois valores para o uso da memória:</span><span class="sxs-lookup"><span data-stu-id="6eb51-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="6eb51-163">Alocado: a quantidade de memória ocupada por objetos gerenciados</span><span class="sxs-lookup"><span data-stu-id="6eb51-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="6eb51-164">[Conjunto de trabalho](/windows/win32/memory/working-set): O conjunto de páginas no espaço de endereço virtual do processo que atualmente residem na memória física.</span><span class="sxs-lookup"><span data-stu-id="6eb51-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="6eb51-165">O conjunto de trabalho mostrado é o mesmo valor exibido pelo Gerenciador de Tarefas.</span><span class="sxs-lookup"><span data-stu-id="6eb51-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="6eb51-166">Objetos transitórios</span><span class="sxs-lookup"><span data-stu-id="6eb51-166">Transient objects</span></span>

<span data-ttu-id="6eb51-167">A API a seguir cria uma instância de string de 10 KB e a devolve ao cliente.</span><span class="sxs-lookup"><span data-stu-id="6eb51-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="6eb51-168">Em cada solicitação, um novo objeto é alocado na memória e escrito para a resposta.</span><span class="sxs-lookup"><span data-stu-id="6eb51-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="6eb51-169">As strings são armazenadas como caracteres UTF-16 em .NET, de modo que cada caractere leva 2 bytes na memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="6eb51-170">O gráfico a seguir é gerado com uma carga relativamente pequena para mostrar como as alocações de memória são impactadas pelo GC.</span><span class="sxs-lookup"><span data-stu-id="6eb51-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![gráfico anterior](memory/_static/bigstring.png)

<span data-ttu-id="6eb51-172">O gráfico anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="6eb51-172">The preceding chart shows:</span></span>

* <span data-ttu-id="6eb51-173">4K RPS (Solicitações por segundo).</span><span class="sxs-lookup"><span data-stu-id="6eb51-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="6eb51-174">As coleções da Geração 0 GC ocorrem a cada dois segundos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="6eb51-175">O conjunto de trabalho é constante em aproximadamente 500 MB.</span><span class="sxs-lookup"><span data-stu-id="6eb51-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="6eb51-176">Cpu é de 12%.</span><span class="sxs-lookup"><span data-stu-id="6eb51-176">CPU is 12%.</span></span>
* <span data-ttu-id="6eb51-177">O consumo de memória e liberação (através de GC) é estável.</span><span class="sxs-lookup"><span data-stu-id="6eb51-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="6eb51-178">O gráfico a seguir é feito no throughput máximo que pode ser manuseado pela máquina.</span><span class="sxs-lookup"><span data-stu-id="6eb51-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![gráfico anterior](memory/_static/bigstring2.png)

<span data-ttu-id="6eb51-180">O gráfico anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="6eb51-180">The preceding chart shows:</span></span>

* <span data-ttu-id="6eb51-181">RPS 22K</span><span class="sxs-lookup"><span data-stu-id="6eb51-181">22K RPS</span></span>
* <span data-ttu-id="6eb51-182">As coleções de geração 0 GC ocorrem várias vezes por segundo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="6eb51-183">As coleções da Geração 1 são acionadas porque o aplicativo alocou significativamente mais memória por segundo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="6eb51-184">O conjunto de trabalho é constante em aproximadamente 500 MB.</span><span class="sxs-lookup"><span data-stu-id="6eb51-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="6eb51-185">A CPU é de 33%.</span><span class="sxs-lookup"><span data-stu-id="6eb51-185">CPU is 33%.</span></span>
* <span data-ttu-id="6eb51-186">O consumo de memória e liberação (através de GC) é estável.</span><span class="sxs-lookup"><span data-stu-id="6eb51-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="6eb51-187">A CPU (33%) não é superutilizada, portanto, a coleta de lixo pode acompanhar um alto número de alocações.</span><span class="sxs-lookup"><span data-stu-id="6eb51-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="6eb51-188">Estação de Trabalho GC vs. Servidor GC</span><span class="sxs-lookup"><span data-stu-id="6eb51-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="6eb51-189">O Coletor de Lixo .NET tem dois modos diferentes:</span><span class="sxs-lookup"><span data-stu-id="6eb51-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="6eb51-190">**Estação de Trabalho GC**: Otimizado para o desktop.</span><span class="sxs-lookup"><span data-stu-id="6eb51-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="6eb51-191">**Servidor GC**.</span><span class="sxs-lookup"><span data-stu-id="6eb51-191">**Server GC**.</span></span> <span data-ttu-id="6eb51-192">O GC padrão para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6eb51-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="6eb51-193">Otimizado para o servidor.</span><span class="sxs-lookup"><span data-stu-id="6eb51-193">Optimized for the server.</span></span>

<span data-ttu-id="6eb51-194">O modo GC pode ser definido explicitamente no arquivo do projeto ou no arquivo *runtimeconfig.json* do aplicativo publicado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="6eb51-195">A marcação a `ServerGarbageCollection` seguir mostra a configuração no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="6eb51-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="6eb51-196">A `ServerGarbageCollection` alteração no arquivo do projeto requer que o aplicativo seja reconstruído.</span><span class="sxs-lookup"><span data-stu-id="6eb51-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="6eb51-197">**Nota:** A coleta de lixo do servidor **não** está disponível em máquinas com um único núcleo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="6eb51-198">Para obter mais informações, consulte <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="6eb51-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="6eb51-199">A imagem a seguir mostra o perfil de memória sob um RPS 5K usando a Estação de Trabalho GC.</span><span class="sxs-lookup"><span data-stu-id="6eb51-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![gráfico anterior](memory/_static/workstation.png)

<span data-ttu-id="6eb51-201">As diferenças entre este gráfico e a versão do servidor são significativas:</span><span class="sxs-lookup"><span data-stu-id="6eb51-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="6eb51-202">O conjunto de trabalho cai de 500 MB para 70 MB.</span><span class="sxs-lookup"><span data-stu-id="6eb51-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="6eb51-203">O GC faz coletas de geração 0 várias vezes por segundo em vez de a cada dois segundos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="6eb51-204">Gc cai de 300 MB para 10 MB.</span><span class="sxs-lookup"><span data-stu-id="6eb51-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="6eb51-205">Em um ambiente típico de servidor web, o uso da CPU é mais importante do que a memória, portanto o Server GC é melhor.</span><span class="sxs-lookup"><span data-stu-id="6eb51-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="6eb51-206">Se a utilização da memória for alta e o uso da CPU for relativamente baixo, o Workstation GC pode ser mais performático.</span><span class="sxs-lookup"><span data-stu-id="6eb51-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="6eb51-207">Por exemplo, alta densidade hospedando vários aplicativos web onde a memória é escassa.</span><span class="sxs-lookup"><span data-stu-id="6eb51-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="6eb51-208">GC usando Docker e pequenos recipientes</span><span class="sxs-lookup"><span data-stu-id="6eb51-208">GC using Docker and small containers</span></span>

<span data-ttu-id="6eb51-209">Quando vários aplicativos em contêiner estão sendo executados em uma máquina, o Workstation GC pode ser mais preformant do que o Server GC.</span><span class="sxs-lookup"><span data-stu-id="6eb51-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="6eb51-210">Para obter mais informações, consulte [Executar com o Servidor GC em um pequeno contêiner](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) e executar com o Servidor GC em um cenário de pequeno contêiner Parte 1 – Limite rígido para o Heap [GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="6eb51-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="6eb51-211">Referências persistentes de objeto</span><span class="sxs-lookup"><span data-stu-id="6eb51-211">Persistent object references</span></span>

<span data-ttu-id="6eb51-212">O GC não pode liberar objetos que são referenciados.</span><span class="sxs-lookup"><span data-stu-id="6eb51-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="6eb51-213">Objetos que são referenciados, mas não são mais necessários, resultam em um vazamento de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="6eb51-214">Se o aplicativo alocar objetos com freqüência e não os liberar depois que eles não forem mais necessários, o uso da memória aumentará com o tempo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="6eb51-215">A API a seguir cria uma instância de string de 10 KB e a devolve ao cliente.</span><span class="sxs-lookup"><span data-stu-id="6eb51-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="6eb51-216">A diferença com o exemplo anterior é que esta instância é referenciada por um membro estático, o que significa que nunca está disponível para coleta.</span><span class="sxs-lookup"><span data-stu-id="6eb51-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="6eb51-217">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6eb51-217">The preceding code:</span></span>

* <span data-ttu-id="6eb51-218">É um exemplo de um típico vazamento de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="6eb51-219">Com chamadas freqüentes, faz com que a `OutOfMemory` memória do aplicativo aumente até que o processo se desfaça com uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6eb51-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![gráfico anterior](memory/_static/eternal.png)

<span data-ttu-id="6eb51-221">Na imagem anterior:</span><span class="sxs-lookup"><span data-stu-id="6eb51-221">In the preceding image:</span></span>

* <span data-ttu-id="6eb51-222">O teste `/api/staticstring` de carga do ponto final causa um aumento linear da memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="6eb51-223">O GC tenta libertar a memória à medida que a pressão da memória cresce, chamando uma coleção de geração 2.</span><span class="sxs-lookup"><span data-stu-id="6eb51-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="6eb51-224">O GC não pode liberar a memória vazada.</span><span class="sxs-lookup"><span data-stu-id="6eb51-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="6eb51-225">Alocado e conjunto de trabalho aumentam com o tempo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="6eb51-226">Alguns cenários, como o cache, exigem que as referências dos objetos sejam mantidas até que a pressão da memória os obrigue a serem liberados.</span><span class="sxs-lookup"><span data-stu-id="6eb51-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="6eb51-227">A <xref:System.WeakReference> classe pode ser usada para este tipo de código de cache.</span><span class="sxs-lookup"><span data-stu-id="6eb51-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="6eb51-228">Um `WeakReference` objeto é coletado sob pressões de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="6eb51-229">A implementação <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`padrão de usos .</span><span class="sxs-lookup"><span data-stu-id="6eb51-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="6eb51-230">Memória nativa</span><span class="sxs-lookup"><span data-stu-id="6eb51-230">Native memory</span></span>

<span data-ttu-id="6eb51-231">Alguns objetos .NET Core dependem da memória nativa.</span><span class="sxs-lookup"><span data-stu-id="6eb51-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="6eb51-232">A memória nativa **não** pode ser coletada pelo GC.</span><span class="sxs-lookup"><span data-stu-id="6eb51-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="6eb51-233">O objeto .NET usando memória nativa deve libertá-lo usando código nativo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="6eb51-234">.NET fornece <xref:System.IDisposable> a interface para permitir que os desenvolvedores liberem a memória nativa.</span><span class="sxs-lookup"><span data-stu-id="6eb51-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="6eb51-235">Mesmo <xref:System.IDisposable.Dispose*> que não seja chamado, `Dispose` as classes corretamente implementadas chamam quando o [finalizador](/dotnet/csharp/programming-guide/classes-and-structs/destructors) é executado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="6eb51-236">Considere o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="6eb51-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="6eb51-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) é uma classe gerenciada, portanto, qualquer instância será coletada no final da solicitação.</span><span class="sxs-lookup"><span data-stu-id="6eb51-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="6eb51-238">A imagem a seguir mostra o `fileprovider` perfil de memória enquanto invoca a API continuamente.</span><span class="sxs-lookup"><span data-stu-id="6eb51-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![gráfico anterior](memory/_static/fileprovider.png)

<span data-ttu-id="6eb51-240">O gráfico anterior mostra um problema óbvio com a implementação desta classe, pois continua aumentando o uso da memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="6eb51-241">Este é um problema conhecido que está sendo rastreado [nesta questão.](https://github.com/dotnet/aspnetcore/issues/3110)</span><span class="sxs-lookup"><span data-stu-id="6eb51-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="6eb51-242">O mesmo vazamento pode acontecer no código do usuário, por um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="6eb51-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="6eb51-243">Não liberando a aula corretamente.</span><span class="sxs-lookup"><span data-stu-id="6eb51-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="6eb51-244">Esquecendo-se `Dispose`de invocar o método dos objetos dependentes que devem ser eliminados.</span><span class="sxs-lookup"><span data-stu-id="6eb51-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="6eb51-245">Grandes objetos amontoam</span><span class="sxs-lookup"><span data-stu-id="6eb51-245">Large objects heap</span></span>

<span data-ttu-id="6eb51-246">A alocação de memória frequente/ciclos livres podem fragmentar a memória, especialmente ao alocar grandes pedaços de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="6eb51-247">Os objetos são alocados em blocos contíguos de memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="6eb51-248">Para mitigar a fragmentação, quando o GC libera a memória, ele tenta desfragmentá-la.</span><span class="sxs-lookup"><span data-stu-id="6eb51-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="6eb51-249">Esse processo é chamado **de compactação.**</span><span class="sxs-lookup"><span data-stu-id="6eb51-249">This process is called **compaction**.</span></span> <span data-ttu-id="6eb51-250">A compactação envolve a movimentação de objetos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-250">Compaction involves moving objects.</span></span> <span data-ttu-id="6eb51-251">Mover objetos grandes impõe uma penalidade de desempenho.</span><span class="sxs-lookup"><span data-stu-id="6eb51-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="6eb51-252">Por esta razão, o GC cria uma zona de memória especial para objetos _grandes,_ chamada [heap de objeto grande](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="6eb51-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="6eb51-253">Objetos maiores que 85.000 bytes (aproximadamente 83 KB) são:</span><span class="sxs-lookup"><span data-stu-id="6eb51-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="6eb51-254">Colocado no LOH.</span><span class="sxs-lookup"><span data-stu-id="6eb51-254">Placed on the LOH.</span></span>
* <span data-ttu-id="6eb51-255">Não compactado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-255">Not compacted.</span></span>
* <span data-ttu-id="6eb51-256">Coletadodurante a geração 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="6eb51-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="6eb51-257">Quando o LOH estiver cheio, o GC acionará uma coleção de geração 2.</span><span class="sxs-lookup"><span data-stu-id="6eb51-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="6eb51-258">Coleções da Geração 2:</span><span class="sxs-lookup"><span data-stu-id="6eb51-258">Generation 2 collections:</span></span>

* <span data-ttu-id="6eb51-259">São inerentemente lentos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-259">Are inherently slow.</span></span>
* <span data-ttu-id="6eb51-260">Além disso, incorrer no custo de desencadear uma coleção em todas as outras gerações.</span><span class="sxs-lookup"><span data-stu-id="6eb51-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="6eb51-261">O código a seguir compacta o LOH imediatamente:</span><span class="sxs-lookup"><span data-stu-id="6eb51-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="6eb51-262">Consulte <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> informações sobre a compactação do LOH.</span><span class="sxs-lookup"><span data-stu-id="6eb51-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="6eb51-263">Em contêineres usando .NET Core 3.0 e posterior, o LOH é automaticamente compactado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="6eb51-264">A seguinte API que ilustra esse comportamento:</span><span class="sxs-lookup"><span data-stu-id="6eb51-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="6eb51-265">O gráfico a seguir mostra `/api/loh/84975` o perfil de memória de chamar o ponto final, sob carga máxima:</span><span class="sxs-lookup"><span data-stu-id="6eb51-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![gráfico anterior](memory/_static/loh1.png)

<span data-ttu-id="6eb51-267">O gráfico a seguir mostra `/api/loh/84976` o perfil de memória de chamar o ponto final, alocando *apenas mais um byte*:</span><span class="sxs-lookup"><span data-stu-id="6eb51-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![gráfico anterior](memory/_static/loh2.png)

<span data-ttu-id="6eb51-269">Nota: `byte[]` A estrutura tem bytes aéreos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="6eb51-270">É por isso que 84.976 bytes acionam o limite de 85.000.</span><span class="sxs-lookup"><span data-stu-id="6eb51-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="6eb51-271">Comparando os dois gráficos anteriores:</span><span class="sxs-lookup"><span data-stu-id="6eb51-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="6eb51-272">O conjunto de trabalho é semelhante para ambos os cenários, cerca de 450 MB.</span><span class="sxs-lookup"><span data-stu-id="6eb51-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="6eb51-273">As solicitações de LOH (84.975 bytes) mostram principalmente coleções de geração 0.</span><span class="sxs-lookup"><span data-stu-id="6eb51-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="6eb51-274">As solicitações over LOH geram coleções constantes de geração 2.</span><span class="sxs-lookup"><span data-stu-id="6eb51-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="6eb51-275">As coleções da geração 2 são caras.</span><span class="sxs-lookup"><span data-stu-id="6eb51-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="6eb51-276">Mais CPU é necessária e o throughput cai quase 50%.</span><span class="sxs-lookup"><span data-stu-id="6eb51-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="6eb51-277">Objetos grandes temporários são particularmente problemáticos porque causam gcs gen2.</span><span class="sxs-lookup"><span data-stu-id="6eb51-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="6eb51-278">Para o máximo desempenho, o uso de objetos grandes deve ser minimizado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="6eb51-279">Se possível, separe objetos grandes.</span><span class="sxs-lookup"><span data-stu-id="6eb51-279">If possible, split up large objects.</span></span> <span data-ttu-id="6eb51-280">Por exemplo, o middleware [Response Caching](xref:performance/caching/response) no ASP.NET Core dividiu as entradas de cache em blocos inferiores a 85.000 bytes.</span><span class="sxs-lookup"><span data-stu-id="6eb51-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="6eb51-281">Os links a seguir mostram a abordagem ASP.NET Core para manter objetos abaixo do limite LOH:</span><span class="sxs-lookup"><span data-stu-id="6eb51-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="6eb51-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="6eb51-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="6eb51-283">ResponseCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="6eb51-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="6eb51-284">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="6eb51-284">For more information, see:</span></span>

* [<span data-ttu-id="6eb51-285">Grande pilha de objetos descoberto</span><span class="sxs-lookup"><span data-stu-id="6eb51-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="6eb51-286">Grande pilha de objetos</span><span class="sxs-lookup"><span data-stu-id="6eb51-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="6eb51-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="6eb51-287">HttpClient</span></span>

<span data-ttu-id="6eb51-288">O uso <xref:System.Net.Http.HttpClient> incorreto pode resultar em um vazamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="6eb51-289">Recursos do sistema, como conexões de banco de dados, soquetes, cabos de arquivos, etc.:</span><span class="sxs-lookup"><span data-stu-id="6eb51-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="6eb51-290">São mais escassos do que a memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="6eb51-291">São mais problemáticos quando vazadas do que a memória.</span><span class="sxs-lookup"><span data-stu-id="6eb51-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="6eb51-292">Desenvolvedores experientes .NET <xref:System.IDisposable.Dispose*> sabem chamar <xref:System.IDisposable>objetos que implementam .</span><span class="sxs-lookup"><span data-stu-id="6eb51-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="6eb51-293">Não descartar objetos `IDisposable` que implementam normalmente resulta em memória vazada ou recursos do sistema vazados.</span><span class="sxs-lookup"><span data-stu-id="6eb51-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="6eb51-294">`HttpClient`implementos `IDisposable`, mas **não** devem ser descartados em cada invocação.</span><span class="sxs-lookup"><span data-stu-id="6eb51-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="6eb51-295">Em `HttpClient` vez disso, deve ser reutilizado.</span><span class="sxs-lookup"><span data-stu-id="6eb51-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="6eb51-296">O ponto final a seguir `HttpClient` cria e elimina uma nova instância em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="6eb51-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="6eb51-297">Em carga, as seguintes mensagens de erro são registradas:</span><span class="sxs-lookup"><span data-stu-id="6eb51-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="6eb51-298">Mesmo que `HttpClient` as instâncias sejam descartadas, a conexão de rede real leva algum tempo para ser liberada pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="6eb51-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="6eb51-299">Ao criar continuamente novas conexões, ocorre _a exaustão das portas._</span><span class="sxs-lookup"><span data-stu-id="6eb51-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="6eb51-300">Cada conexão com o cliente requer sua própria porta de cliente.</span><span class="sxs-lookup"><span data-stu-id="6eb51-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="6eb51-301">Uma maneira de evitar a exaustão `HttpClient` da porta é reutilizar a mesma instância:</span><span class="sxs-lookup"><span data-stu-id="6eb51-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="6eb51-302">A `HttpClient` instância é liberada quando o aplicativo pára.</span><span class="sxs-lookup"><span data-stu-id="6eb51-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="6eb51-303">Este exemplo mostra que nem todos os recursos descartáveis devem ser eliminados após cada uso.</span><span class="sxs-lookup"><span data-stu-id="6eb51-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="6eb51-304">Veja a seguir uma melhor maneira de `HttpClient` lidar com a vida útil de uma instância:</span><span class="sxs-lookup"><span data-stu-id="6eb51-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="6eb51-305">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="6eb51-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="6eb51-306">BLOG DE FÁBRICA HTTPClient</span><span class="sxs-lookup"><span data-stu-id="6eb51-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="6eb51-307">Agrupamento de objetos</span><span class="sxs-lookup"><span data-stu-id="6eb51-307">Object pooling</span></span>

<span data-ttu-id="6eb51-308">O exemplo anterior `HttpClient` mostrou como a instância pode ser feita estática e reutilizada por todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="6eb51-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="6eb51-309">A reutilização evita ficar sem recursos.</span><span class="sxs-lookup"><span data-stu-id="6eb51-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="6eb51-310">Agrupamento de objetos:</span><span class="sxs-lookup"><span data-stu-id="6eb51-310">Object pooling:</span></span>

* <span data-ttu-id="6eb51-311">Usa o padrão de reutilização.</span><span class="sxs-lookup"><span data-stu-id="6eb51-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="6eb51-312">É projetado para objetos que são caros de criar.</span><span class="sxs-lookup"><span data-stu-id="6eb51-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="6eb51-313">Uma piscina é uma coleção de objetos pré-inicializados que podem ser reservados e liberados através de threads.</span><span class="sxs-lookup"><span data-stu-id="6eb51-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="6eb51-314">Os pools podem definir regras de alocação, como limites, tamanhos predefinidos ou taxa de crescimento.</span><span class="sxs-lookup"><span data-stu-id="6eb51-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="6eb51-315">O pacote NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contém classes que ajudam a gerenciar tais pools.</span><span class="sxs-lookup"><span data-stu-id="6eb51-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="6eb51-316">O ponto final da API `byte` a seguir instancia um buffer preenchido com números aleatórios em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="6eb51-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="6eb51-317">O gráfico a seguir chama a API anterior com carga moderada:</span><span class="sxs-lookup"><span data-stu-id="6eb51-317">The following chart display calling the preceding API with moderate load:</span></span>

![gráfico anterior](memory/_static/array.png)

<span data-ttu-id="6eb51-319">No gráfico anterior, as coleções da geração 0 acontecem aproximadamente uma vez por segundo.</span><span class="sxs-lookup"><span data-stu-id="6eb51-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="6eb51-320">O código anterior pode ser otimizado agrupando o `byte` buffer usando [arrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="6eb51-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="6eb51-321">Uma instância estática é reutilizada entre as solicitações.</span><span class="sxs-lookup"><span data-stu-id="6eb51-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="6eb51-322">O que é diferente com essa abordagem é que um objeto agrupado é devolvido da API.</span><span class="sxs-lookup"><span data-stu-id="6eb51-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="6eb51-323">Isso significa:</span><span class="sxs-lookup"><span data-stu-id="6eb51-323">That means:</span></span>

* <span data-ttu-id="6eb51-324">O objeto está fora de seu controle assim que você voltar do método.</span><span class="sxs-lookup"><span data-stu-id="6eb51-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="6eb51-325">Você não pode liberar o objeto.</span><span class="sxs-lookup"><span data-stu-id="6eb51-325">You can't release the object.</span></span>

<span data-ttu-id="6eb51-326">Para configurar a eliminação do objeto:</span><span class="sxs-lookup"><span data-stu-id="6eb51-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="6eb51-327">Encapsular a matriz agrupada em um objeto descartável.</span><span class="sxs-lookup"><span data-stu-id="6eb51-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="6eb51-328">Registre o objeto pooled com [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="6eb51-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="6eb51-329">`RegisterForDispose`cuidará de `Dispose`chamar o objeto de destino para que ele só seja liberado quando a solicitação HTTP estiver concluída.</span><span class="sxs-lookup"><span data-stu-id="6eb51-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="6eb51-330">Aplicar a mesma carga que a versão não agrupada resulta no gráfico a seguir:</span><span class="sxs-lookup"><span data-stu-id="6eb51-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![gráfico anterior](memory/_static/pooledarray.png)

<span data-ttu-id="6eb51-332">A principal diferença é a alocação de bytes, e como conseqüência muito menos coleções de geração 0.</span><span class="sxs-lookup"><span data-stu-id="6eb51-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6eb51-333">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6eb51-333">Additional resources</span></span>

* [<span data-ttu-id="6eb51-334">Coleta de lixo</span><span class="sxs-lookup"><span data-stu-id="6eb51-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="6eb51-335">Entendendo diferentes modos gc com o Simultcurrency Visualizer</span><span class="sxs-lookup"><span data-stu-id="6eb51-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="6eb51-336">Grande pilha de objetos descoberto</span><span class="sxs-lookup"><span data-stu-id="6eb51-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="6eb51-337">Grande pilha de objetos</span><span class="sxs-lookup"><span data-stu-id="6eb51-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
