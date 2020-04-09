---
title: Tarefas em segundo plano com serviços hospedados no ASP.NET Core
author: rick-anderson
description: Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666197"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Tarefas em segundo plano com serviços hospedados no ASP.NET Core

Por [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*. Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Este tópico fornece três exemplos de serviço hospedado:

* Tarefa em segundo plano que é executada com um temporizador.
* Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes). O serviço escopo pode usar [injeção de dependência (DI)](xref:fundamentals/dependency-injection).
* Tarefas em segundo plano na fila que são executadas sequencialmente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Modelo de serviço de trabalho

O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada. Um aplicativo criado a partir do modelo serviço ao trabalhador especifica o SDK do trabalhador em seu arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Para usar o modelo como base para um aplicativo de serviços hospedados:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Pacote

Um aplicativo baseado no modelo `Microsoft.NET.Sdk.Worker` de Serviço ao Trabalhador usa o SDK e tem uma referência explícita de pacote ao pacote [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) Por exemplo, consulte o arquivo de projeto do aplicativo de amostra *(BackgroundTasksSample.csproj*).

Para aplicativos web `Microsoft.NET.Sdk.Web` que usam o SDK, o pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) é referenciado implicitamente a partir da estrutura compartilhada. Uma referência explícita de pacote no arquivo de projeto do aplicativo não é necessária.

## <a name="ihostedservice-interface"></a>Interface IHostedService

A <xref:Microsoft.Extensions.Hosting.IHostedService> interface define dois métodos para objetos gerenciados pelo host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano. `StartAsync`é chamado *antes:*

  * O pipeline de processamento de solicitações`Startup.Configure`do aplicativo está configurado ().
  * O servidor é iniciado e [iApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é acionado.

  O comportamento padrão pode ser alterado para que `StartAsync` o serviço hospedado seja executado após `ApplicationStarted` a configuração do pipeline do aplicativo e seja chamado. Para alterar o comportamento padrão, adicione`VideosWatcher` o serviço hospedado `ConfigureWebHostDefaults`(no exemplo a seguir) após a chamada :

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal. `StopAsync` contém a lógica para encerrar a tarefa em segundo plano. Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.

  O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal. Quando for solicitado um cancelamento no token:

  * Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.
  * Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.

  No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.

  Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado. Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.

  Para estender o tempo limite de desligamento padrão de cinco segundos, defina:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico. Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web. Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.

O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo. Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.

## <a name="backgroundservice-base-class"></a>Classe base backgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>é uma classe base para <xref:Microsoft.Extensions.Hosting.IHostedService>implementar uma longa execução.

[ExecuteAsync(CancelamentoToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) é chamado para executar o serviço em segundo plano. A implementação <xref:System.Threading.Tasks.Task> retorna um que representa toda a vida útil do serviço de fundo. Nenhum outro serviço é iniciado até [que o ExecuteAsync se torne assíncrono,](https://github.com/dotnet/extensions/issues/2149)como por meio da chamada `await`. Evite realizar trabalhos longos `ExecuteAsync`de inicialização em . Os blocos de host em [StopAsync (CancelamentoToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) aguardam para `ExecuteAsync` serem concluídos.

O token de cancelamento é acionado quando [o IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) é chamado. Sua implementação deve `ExecuteAsync` ser concluída prontamente quando o token de cancelamento for acionado para encerrar graciosamente o serviço. Caso contrário, o serviço desgraciosamente desliga-se no tempo de desligamento. Para obter mais informações, consulte a seção [interface IHostedService.](#ihostedservice-interface)

## <a name="timed-background-tasks"></a>Tarefas em segundo plano temporizadas

Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer). O temporizador dispara o método `DoWork` da tarefa. O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Não <xref:System.Threading.Timer> se espera que execuções anteriores `DoWork` terminem, então a abordagem mostrada pode não ser adequada para todos os cenários. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) é usado para incrementar o contador de execução como uma `executionCount` operação atômica, o que garante que vários segmentos não atualizem simultaneamente.

O serviço é `IHostBuilder.ConfigureServices` registrado em ( `AddHostedService` *Program.cs*) com o método de extensão:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumindo um serviço com escopo em uma tarefa em segundo plano

Para usar [serviços escopo dentro](xref:fundamentals/dependency-injection#service-lifetimes) de um [BackgroundService,](#backgroundservice-base-class)crie um escopo. Por padrão, nenhum escopo é criado para um serviço hospedado.

O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano. No exemplo a seguir:

* O serviço é assíncrono. O método `DoWork` retorna um `Task`. Para fins de demonstração, é esperado `DoWork` um atraso de dez segundos no método.
* Um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

O serviço hospedado cria um escopo para resolver o `DoWork` serviço de tarefa de fundo escopo para chamar seu método. `DoWork`retorna `Task`um , que `ExecuteAsync`é aguardado em:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Os serviços estão `IHostBuilder.ConfigureServices` registrados em (*Program.cs).* O serviço hospedado é registrado `AddHostedService` com o método de extensão:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tarefas em segundo plano na fila

Uma fila de tarefas de fundo é <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> baseada no .NET 4.x[(provisoriamente programado para ser incorporado para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

No exemplo `QueueHostedService` a seguir:

* O `BackgroundProcessing` método `Task`retorna a , `ExecuteAsync`que é aguardado em .
* As tarefas de fundo na fila são `BackgroundProcessing`enfileiradas e executadas em .
* Os itens de trabalho são aguardados `StopAsync`antes que o serviço pare em .

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Um `MonitorLoop` serviço lida com tarefas de enfileiramento `w` para o serviço hospedado sempre que a chave é selecionada em um dispositivo de entrada:

* O `IBackgroundTaskQueue` é injetado `MonitorLoop` no serviço.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`é chamado para enfileirar um item de trabalho.
* O item de trabalho simula uma tarefa de fundo de longa duração:
  * Três atrasos de 5 segundos`Task.Delay`são executados ( ).
  * Uma `try-catch` declaração <xref:System.OperationCanceledException> é armadilha se a tarefa for cancelada.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Os serviços estão `IHostBuilder.ConfigureServices` registrados em (*Program.cs).* O serviço hospedado é registrado `AddHostedService` com o método de extensão:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop`é iniciado `Program.Main`em:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*. Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Este tópico fornece três exemplos de serviço hospedado:

* Tarefa em segundo plano que é executada com um temporizador.
* Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes). O serviço escopo pode usar [injeção de dependência (DI)](xref:fundamentals/dependency-injection)
* Tarefas em segundo plano na fila que são executadas sequencialmente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pacote

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interface IHostedService

Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. A interface define dois métodos para objetos que são gerenciados pelo host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano. Ao usar o `StartAsync` [Web Host,](xref:fundamentals/host/web-host)é chamado depois que o servidor é iniciado e [iApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é acionado. Ao usar o `StartAsync` [Host Genérico,](xref:fundamentals/host/generic-host)é chamado antes `ApplicationStarted` de ser acionado.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal. `StopAsync` contém a lógica para encerrar a tarefa em segundo plano. Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.

  O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal. Quando for solicitado um cancelamento no token:

  * Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.
  * Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.

  No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.

  Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado. Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.

  Para estender o tempo limite de desligamento padrão de cinco segundos, defina:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico. Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web. Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.

O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo. Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.

## <a name="timed-background-tasks"></a>Tarefas em segundo plano temporizadas

Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer). O temporizador dispara o método `DoWork` da tarefa. O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Não <xref:System.Threading.Timer> se espera que execuções anteriores `DoWork` terminem, então a abordagem mostrada pode não ser adequada para todos os cenários.

O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumindo um serviço com escopo em uma tarefa em segundo plano

Para usar [serviços](xref:fundamentals/dependency-injection#service-lifetimes) `IHostedService`escopo dentro de um , crie um escopo. Por padrão, nenhum escopo é criado para um serviço hospedado.

O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano. No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Os serviços são registrados em `Startup.ConfigureServices`. A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tarefas em segundo plano na fila

Uma fila de tarefas de fundo é <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> baseada no .NET Framework 4.x[(provisoriamente programado para ser incorporado para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um [BackgroundService](#backgroundservice-base-class), que é uma classe base para implementar um `IHostedService` de longa execução:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Os serviços são registrados em `Startup.ConfigureServices`. A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Na classe de modelo de página de índice:

* O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.
* Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`. O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo. Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado. `QueueBackgroundWorkItem`é chamado para enfileirar um item de trabalho:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Execute tarefas em segundo plano com webJobs no Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
