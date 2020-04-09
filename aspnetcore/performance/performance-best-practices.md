---
title: ASP.NET principais práticas de desempenho
author: mjrousos
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core e evitar problemas comuns de desempenho.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977178"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET principais práticas de desempenho

Por [Mike Rousos](https://github.com/mjrousos)

Este artigo fornece diretrizes para práticas recomendadas de desempenho com ASP.NET Core.

## <a name="cache-aggressively"></a>Cache agressivamente

O caching é discutido em várias partes deste documento. Para obter mais informações, consulte <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Entenda os caminhos do código quente

Neste documento, um *caminho de código quente* é definido como um caminho de código que é frequentemente chamado e onde grande parte do tempo de execução ocorre. Caminhos de código quente normalmente limitam a escala e o desempenho do aplicativo e são discutidos em várias partes deste documento.

## <a name="avoid-blocking-calls"></a>Evite bloquear chamadas

ASP.NET os aplicativos Core devem ser projetados para processar muitas solicitações simultaneamente. As APIs assíncronas permitem que um pequeno pool de threads acuse milhares de solicitações simultâneas, não esperando o bloqueio de chamadas. Em vez de esperar uma tarefa síncrona de longa duração para ser concluída, o segmento pode funcionar em outra solicitação.

Um problema de desempenho comum em ASP.NET aplicativos Core é bloquear chamadas que podem ser assíncronas. Muitas chamadas de bloqueio síncrono levam à [fome do Thread Pool](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) e aos tempos de resposta degradados.

**Não:**

* Bloqueie a execução assíncrona chamando [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) ou [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Adquira bloqueios em caminhos de código comuns. ASP.NET os aplicativos Core são mais performáticos quando projetados para executar código em paralelo.
* Ligue para [Task.Run](/dotnet/api/system.threading.tasks.task.run) e aguarde imediatamente. ASP.NET Core já executa o código do aplicativo em threads normais do Thread Pool, portanto, chamar Task.Run só resulta em agendamento extra desnecessário do Pool de Threads. Mesmo que o código programado bloqueie um segmento, o Task.Run não impede isso.

**Faça:**

* Faça [caminhos de código quente](#understand-hot-code-paths) assíncronos.
* As APIs de acesso a dados, I/O e operações de longa duração são assíncronas se uma API assíncrona estiver disponível. **Não** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) para fazer uma API síncrona sincronizada.
* Torne as ações do controlador/Razor Page assíncronas. Toda a pilha de chamadas é assíncrona para se beneficiar de padrões [assíncronos/aguardados.](/dotnet/csharp/programming-guide/concepts/async/)

Um profiler, como [o PerfView,](https://github.com/Microsoft/perfview)pode ser usado para encontrar threads frequentemente adicionados ao [Pool de segmentos](/windows/desktop/procthread/thread-pools). O `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` evento indica um segmento adicionado ao pool de segmentos. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimizar grandes alocações de objetos

O [coletor de lixo .NET Core](/dotnet/standard/garbage-collection/) gerencia a alocação e a liberação de memória automaticamente em ASP.NET aplicativos Core. A coleta automática de lixo geralmente significa que os desenvolvedores não precisam se preocupar sobre como ou quando a memória é liberada. No entanto, limpar objetos não referenciados leva tempo da CPU, então os desenvolvedores devem minimizar a alocação de objetos em [caminhos de código quente](#understand-hot-code-paths). A coleta de lixo é especialmente cara em objetos grandes (> 85 K bytes). Objetos grandes são armazenados no [grande monte de objetos](/dotnet/standard/garbage-collection/large-object-heap) e requerem uma coleta completa (geração 2) de lixo para limpar. Ao contrário das coleções de geração 0 e geração 1, uma coleção de geração 2 requer uma suspensão temporária da execução do aplicativo. A alocação freqüente e a desalocação de objetos grandes podem causar desempenho inconsistente.

Recomendações:

* **Considere** o cache de objetos grandes que são usados com freqüência. O cache de objetos grandes evita alocações caras.
* **Faça** buffers de pool usando um [arrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) para armazenar grandes arrays.
* **Não** aloque muitos objetos grandes de curta duração em [caminhos de código quente.](#understand-hot-code-paths)

Problemas de memória, como o anterior, podem ser diagnosticados revisando estatísticas de coleta de lixo (GC) no [PerfView](https://github.com/Microsoft/perfview) e examinando:

* Tempo de pausa da coleta de lixo.
* Qual a porcentagem do tempo do processador gasto na coleta de lixo.
* Quantas coletas de lixo são geração 0, 1 e 2.

Para obter mais informações, consulte [Coleta e Desempenho do Lixo](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Otimizar o acesso a dados e I/O

Interações com um armazenamento de dados e outros serviços remotos são muitas vezes as partes mais lentas de um aplicativo ASP.NET Core. Ler e escrever dados de forma eficiente é fundamental para um bom desempenho.

Recomendações:

* **Chame** todas as APIs de acesso de dados de forma assíncrona.
* **Não** recupere mais dados do que o necessário. Escreva consultas para retornar apenas os dados necessários para a solicitação HTTP atual.
* **Considere** o cache de dados acessados com freqüência recuperados de um banco de dados ou serviço remoto se dados ligeiramente desatualizados forem aceitáveis. Dependendo do cenário, use um [MemoryCache](xref:performance/caching/memory) ou um [DistributedCache](xref:performance/caching/distributed). Para obter mais informações, consulte <xref:performance/caching/response>.
* **Minimize** as viagens de ida e volta da rede. O objetivo é recuperar os dados necessários em uma única chamada, em vez de várias chamadas.
* **Use** [consultas sem rastreamento](/ef/core/querying/tracking#no-tracking-queries) no Entity Framework Core ao acessar dados para fins somente leitura. O EF Core pode retornar os resultados de consultas sem rastreamento de forma mais eficiente.
* **Filtrar** e agregar consultas LINQ (com `.Where`, `.Select`ou `.Sum` instruções, por exemplo) para que a filtragem seja realizada pelo banco de dados.
* **Considere** que o EF Core resolve alguns operadores de consulta no cliente, o que pode levar a uma execução de consulta ineficiente. Para obter mais informações, consulte [problemas de desempenho da avaliação do cliente](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Não use** consultas de projeção em coleções, o que pode resultar na execução de consultas SQL "N + 1". Para obter mais informações, consulte [Otimização de subconsultas correlacionadas](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Consulte [o EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) para abordagens que podem melhorar o desempenho em aplicativos de alta escala:

* [Pool de DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Consultas explicitamente compiladas](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Recomendamos medir o impacto das abordagens de alto desempenho anteriores antes de comprometer a base de código. A complexidade adicional das consultas compiladas pode não justificar a melhoria do desempenho.

Os problemas de consulta podem ser detectados revisando o tempo gasto acessando dados com [o Application Insights](/azure/application-insights/app-insights-overview) ou com ferramentas de criação de perfil. A maioria dos bancos de dados também disponibiliza estatísticas sobre consultas frequentemente executadas.

## <a name="pool-http-connections-with-httpclientfactory"></a>Pool conexões HTTP com httpClientFactory

Embora [o HttpClient](/dotnet/api/system.net.http.httpclient) implemente a `IDisposable` interface, ela foi projetada para reutilização. As `HttpClient` instâncias fechadas `TIME_WAIT` deixam os soquetes abertos no estado por um curto período de tempo. Se um caminho de `HttpClient` código que cria e elimina objetos é usado com freqüência, o aplicativo pode esgotar os soquetes disponíveis. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) foi introduzido no ASP.NET Core 2.1 como uma solução para este problema. Ele lida com o pool de conexões HTTP para otimizar o desempenho e a confiabilidade.

Recomendações:

* **Não** crie e `HttpClient` elimine diretamente as instâncias.
* **Use** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) para `HttpClient` recuperar instâncias. Para obter mais informações, consulte [Use HttpClientFactory para implementar solicitações HTTP resilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Mantenha os caminhos de código comuns rapidamente

Você quer que todos os seus códigos sejam rápidos. Os caminhos de código frequentemente chamados são os mais críticos para otimizar. Eles incluem:

* Os componentes de middleware no pipeline de processamento de solicitação do aplicativo, especialmente middleware, são executados no início do pipeline. Esses componentes têm um grande impacto no desempenho.
* Código que é executado para cada solicitação ou várias vezes por solicitação. Por exemplo, registro personalizado, manipuladores de autorização ou inicialização de serviços transitórios.

Recomendações:

* **Não** use componentes de middleware personalizados com tarefas de longa duração.
* **Use** ferramentas de criação de perfil de desempenho, como [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) ou [PerfView),](https://github.com/Microsoft/perfview)para identificar [caminhos de código quente](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Concluir tarefas de longa duração fora das solicitações HTTP

A maioria das solicitações para um aplicativo ASP.NET Core pode ser tratada por um controlador ou modelo de página chamando os serviços necessários e retornando uma resposta HTTP. Para algumas solicitações que envolvem tarefas de longa duração, é melhor tornar todo o processo de solicitação-resposta assíncrono.

Recomendações:

* **Não** espere que tarefas de longa duração sejam concluídas como parte do processamento de solicitação HTTP comum.
* **Considere** lidar com solicitações de longa duração com [serviços](xref:fundamentals/host/hosted-services) de fundo ou fora de processo com uma [função Azure](/azure/azure-functions/). Completar o trabalho fora do processo é especialmente benéfico para tarefas intensivas em CPU.
* **Use** opções de comunicação em [SignalR](xref:signalr/introduction)tempo real, como, para se comunicar com os clientes de forma assíncrona.

## <a name="minify-client-assets"></a>Minimizar ativos do cliente

ASP.NET aplicativos Core com front-ends complexos frequentemente servem muitos arquivos JavaScript, CSS ou imagem. O desempenho das solicitações iniciais de carga pode ser melhorado por:

* Agrupamento, que combina vários arquivos em um.
* Minifying, que reduz o tamanho dos arquivos removendo espaço em branco e comentários.

Recomendações:

* **Use** o suporte [incorporado](xref:client-side/bundling-and-minification) do ASP.NET Core para agrupar e minificar ativos do cliente.
* **Considere** outras ferramentas de terceiros, como [o Webpack,](https://webpack.js.org/)para gerenciamento complexo de ativos de clientes.

## <a name="compress-responses"></a>Compactar respostas

 Reduzir o tamanho da resposta geralmente aumenta a capacidade de resposta de um aplicativo, muitas vezes de forma dramática. Uma maneira de reduzir o tamanho da carga é comprimir as respostas de um aplicativo. Para obter mais informações, consulte [compressão de resposta](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Use a versão mais recente do ASP.NET Core

Cada nova versão do ASP.NET Core inclui melhorias de desempenho. Otimizações no .NET Core e ASP.NET Core significam que as versões mais recentes geralmente superam as versões mais antigas. Por exemplo, o .NET Core 2.1 adicionou suporte para expressões regulares compiladas e se beneficiou do [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2.2 adicionou suporte para HTTP/2. [ASP.NET Core 3.0 adiciona muitas melhorias](xref:aspnetcore-3.0) que reduzem o uso da memória e melhoram o throughput. Se o desempenho for uma prioridade, considere atualizar para a versão atual do ASP.NET Core.

## <a name="minimize-exceptions"></a>Minimizar exceções

Exceções devem ser raras. Lançar e capturar exceções é lento em relação a outros padrões de fluxo de código. Por causa disso, exceções não devem ser usadas para controlar o fluxo normal do programa.

Recomendações:

* **Não** use o arremesso ou a captura de exceções como um meio de fluxo normal do programa, especialmente em [caminhos de código quente](#understand-hot-code-paths).
* **Inclua** a lógica no aplicativo para detectar e lidar com condições que causariam uma exceção.
* **Faça** exceções de arremesso ou captura para condições incomuns ou inesperadas.

Ferramentas de diagnóstico de aplicativos, como o Application Insights, podem ajudar a identificar exceções comuns em um aplicativo que podem afetar o desempenho.

## <a name="performance-and-reliability"></a>Desempenho e confiabilidade

As seções a seguir fornecem dicas de desempenho e problemas e soluções de confiabilidade conhecidos.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Evite leitura síncrona ou gravação no corpo HttpRequest/HttpResponse

Tudo o que há de I/O no ASP.NET Core é assíncrono. Os servidores `Stream` implementam a interface, que tem sobrecargas síncronas e assíncronas. Os assíncronos devem ser preferidos para evitar o bloqueio de roscas de rosca. O bloqueio de roscas pode levar à fome da piscina de fios.

**Não faça isso:** O exemplo a <xref:System.IO.StreamReader.ReadToEnd*>seguir usa o . Bloqueia o fio atual para esperar pelo resultado. Este é um exemplo de [sincronização sobre assincronização](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

No código anterior, `Get` lê sincronizadamente todo o corpo de solicitação HTTP na memória. Se o cliente estiver fazendo upload lento, o aplicativo está fazendo sincronização sobre a sincronia. O aplicativo faz sincronia sobre assincronização porque kestrel **NÃO** suporta leituras síncronas.

**Faça isso:** O exemplo <xref:System.IO.StreamReader.ReadToEndAsync*> a seguir usa e não bloqueia o fio durante a leitura.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

O código anterior lê assíncronamente todo o corpo de solicitação HTTP na memória.

> [!WARNING]
> Se a solicitação for grande, ler todo o corpo de solicitação HTTP na memória pode levar a uma condição fora da memória (OOM). OOM pode resultar em uma negação de serviço.  Para obter mais informações, consulte [Evite ler grandes corpos de solicitação ou corpos de resposta](#arlb) na memória neste documento.

**Faça isso:** O exemplo a seguir é totalmente assíncrono usando um órgão de solicitação não tamponado:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

O código anterior desserializa assíncronamente o corpo de solicitação em um objeto C#.

## <a name="prefer-readformasync-over-requestform"></a>Prefira ReadFormAsync ao solicitar.formulário

Use `HttpContext.Request.ReadFormAsync` em vez de `HttpContext.Request.Form`.
`HttpContext.Request.Form`pode ser lido com segurança apenas com as seguintes condições:

* O formulário foi lido por `ReadFormAsync`uma chamada para , e
* O valor do formulário armazenado em cache está sendo lido usando`HttpContext.Request.Form`

**Não faça isso:** O exemplo `HttpContext.Request.Form`a seguir usa .  `HttpContext.Request.Form`usa [sincronização sobre assincronização](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) e pode levar à fome de pool de segmentos.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Faça isso:** O exemplo `HttpContext.Request.ReadFormAsync` a seguir é usa para ler o corpo do formulário assíncronamente.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Evite ler grandes corpos de solicitação ou corpos de resposta na memória

Em .NET, cada alocação de objeto superior a 85 KB acaba no heap de objeto grande[(LOH).](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/) Objetos grandes são caros de duas maneiras:

* O custo de alocação é alto porque a memória de um objeto grande recém-alocado tem que ser limpa. O CLR garante que a memória para todos os objetos recém-alocados seja limpa.
* LOH é coletado com o resto do monte. Loh requer uma coleta completa [de lixo](/dotnet/standard/garbage-collection/fundamentals) ou [coleta Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Este [post descreve](https://adamsitnik.com/Array-Pool/#the-problem) o problema de forma sucinta:

> Quando um objeto grande é alocado, ele é marcado como objeto Gen 2. Não gen 0 como para objetos pequenos. As conseqüências são que se você ficar sem memória em LOH, GC limpa todo o monte gerenciado, não apenas LOH. Então limpa gen 0, Gen 1 e Gen 2, incluindo LOH. Isso é chamado de coleta completa de lixo e é a coleta de lixo mais demorada. Para muitas aplicações, pode ser aceitável. Mas definitivamente não para servidores web de alto desempenho, onde poucos buffers de memória grandes são necessários para lidar com uma solicitação média da Web (leia de um soquete, descompactação, decodificar json & mais).

Ingenuamente armazenando um grande pedido `byte[]` ou `string`corpo de resposta em um único ou:

* Pode resultar em ficar sem espaço rapidamente no LOH.
* Pode causar problemas de desempenho para o aplicativo por causa da execução de GCs completos.

## <a name="working-with-a-synchronous-data-processing-api"></a>Trabalhando com uma API de processamento de dados síncrono

Ao usar um serializador/desserializador que só suporta leituras e gravações síncronas (por exemplo, [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Tampone os dados na memória de forma assíncrona antes de passá-los para o serializador/desserializador.

> [!WARNING]
> Se a solicitação for grande, pode levar a uma condição fora da memória (OOM). OOM pode resultar em uma negação de serviço.  Para obter mais informações, consulte [Evite ler grandes corpos de solicitação ou corpos de resposta](#arlb) na memória neste documento.

ASP.NET Core 3.0 usa <xref:System.Text.Json> por padrão para serialização JSON. <xref:System.Text.Json>:

* Lê e escreve JSON assíncronamente.
* É otimizado para texto UTF-8.
* Tipicamente desempenho `Newtonsoft.Json`superior a .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Não armazene IHttpContext.HttpContext em um campo

O [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` retorna a solicitação ativa quando acessada a partir do segmento de solicitação. O `IHttpContextAccessor.HttpContext` **não** deve ser armazenado em um campo ou variável.

**Não faça isso:** O exemplo a `HttpContext` seguir armazena o em um campo e, em seguida, tenta usá-lo mais tarde.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

O código anterior captura frequentemente um `HttpContext` nulo ou incorreto no construtor.

**Faça isso:** O seguinte exemplo:

* Armazena <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> o em um campo.
* Usa `HttpContext` o campo na hora `null`correta e verifica se .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Não acesse httpcontext a partir de vários threads

`HttpContext`*NÃO* é seguro para rosca. O `HttpContext` acesso a partir de vários threads em paralelo pode resultar em comportamentos indefinidos, como travamentos, travas e corrupção de dados.

**Não faça isso:** O exemplo a seguir faz três solicitações paralelas e registra o caminho de solicitação de entrada antes e depois da solicitação HTTP de saída. O caminho de solicitação é acessado a partir de vários segmentos, potencialmente em paralelo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Faça isso:** O exemplo a seguir copia todos os dados da solicitação recebida antes de fazer as três solicitações paralelas.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Não use o HttpContext após a solicitação ser concluída

`HttpContext`só é válido enquanto houver uma solicitação HTTP ativa no pipeline ASP.NET Core. Todo o ASP.NET Core pipeline é uma cadeia assíncrona de delegados que executa cada solicitação. Quando `Task` o retorno desta cadeia é `HttpContext` concluído, o é reciclado.

**Não faça isso:** O exemplo `async void` a seguir usa que faz `await` com que a solicitação HTTP seja concluída quando a primeira é atingida:

* O que **é sempre** uma má prática em ASP.NET aplicativos Core.
* Acessa `HttpResponse` o após a solicitação HTTP ser concluída.
* Trava o processo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Faça isso:** O exemplo a `Task` seguir retorna a para a estrutura, de modo que a solicitação HTTP não é concluída até que a ação seja concluída.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Não capture o HttpContext em segmentos de fundo

**Não faça isso:** O exemplo a seguir mostra `HttpContext` que `Controller` um fechamento é capturar a propriedade. Esta é uma má prática porque o item de trabalho poderia:

* Corra para fora do escopo de solicitação.
* Tente ler `HttpContext`errado.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Faça isso:** O seguinte exemplo:

* Copia os dados necessários na tarefa de fundo durante a solicitação.
* Não faz referência a nada do controlador.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

As tarefas de fundo devem ser implementadas como serviços hospedados. Saiba mais em [Tarefas em segundo plano com serviços hospedados](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Não capture serviços injetados nos controladores em segmentos de fundo

**Não faça isso:** O exemplo a seguir mostra `DbContext` que `Controller` um encerramento é capturar o parâmetro de ação. Isso é uma má prática.  O item de trabalho pode ser executado fora do escopo de solicitação. O `ContosoDbContext` é escopo para a solicitação, `ObjectDisposedException`resultando em um .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Faça isso:** O seguinte exemplo:

* Injeta um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> para criar um escopo no item de trabalho de fundo. `IServiceScopeFactory`é um singleton.
* Cria um novo escopo de injeção de dependência no segmento de fundo.
* Não faz referência a nada do controlador.
* Não captura o `ContosoDbContext` pedido de entrada.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

O seguinte código destacado:

* Cria um escopo para a vida útil da operação de fundo e resolve os serviços a partir dela.
* Usa `ContosoDbContext` do escopo correto.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Não modifique o código de status ou os cabeçalhos depois que o corpo de resposta tiver iniciado

ASP.NET Core não tampona o corpo de resposta HTTP. A primeira vez que a resposta é escrita:

* Os cabeçalhos são enviados junto com esse pedaço do corpo para o cliente.
* Não é mais possível mudar os cabeçalhos de resposta.

**Não faça isso:** O código a seguir tenta adicionar cabeçalhos de resposta depois que a resposta já foi iniciada:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

No código anterior, `context.Response.Headers["test"] = "test value";` lançará `next()` uma exceção se tiver escrito para a resposta.

**Faça isso:** O exemplo a seguir verifica se a resposta HTTP foi iniciada antes de modificar os cabeçalhos.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Faça isso:** O exemplo `HttpResponse.OnStarting` a seguir é usa para definir os cabeçalhos antes que os cabeçalhos de resposta sejam liberados para o cliente.

Verificar se a resposta não foi iniciada permite registrar um retorno de chamada que será invocado pouco antes dos cabeçalhos de resposta serem escritos. Verificando se a resposta não foi iniciada:

* Fornece a capacidade de anexar ou substituir cabeçalhos bem a tempo.
* Não requer conhecimento do próximo middleware no oleoduto.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Não ligue para o próximo() se você já começou a escrever para o corpo de resposta

Os componentes só esperam ser chamados se for possível manuseá-los e manipular a resposta.
