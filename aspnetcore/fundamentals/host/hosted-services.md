---
<span data-ttu-id="089ef-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="089ef-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="089ef-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="089ef-102">'Blazor'</span></span>
- <span data-ttu-id="089ef-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="089ef-103">'Identity'</span></span>
- <span data-ttu-id="089ef-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="089ef-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="089ef-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="089ef-105">'Razor'</span></span>
- <span data-ttu-id="089ef-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="089ef-106">'SignalR' uid:</span></span> 

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="089ef-107">Tarefas em segundo plano com serviços hospedados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="089ef-107">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="089ef-108">Por [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="089ef-108">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="089ef-109">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="089ef-109">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="089ef-110">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="089ef-110">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="089ef-111">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="089ef-111">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="089ef-112">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="089ef-112">Background task that runs on a timer.</span></span>
* <span data-ttu-id="089ef-113">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="089ef-113">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="089ef-114">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="089ef-114">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="089ef-115">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="089ef-115">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="089ef-116">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="089ef-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="089ef-117">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="089ef-117">Worker Service template</span></span>

<span data-ttu-id="089ef-118">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="089ef-118">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="089ef-119">Um aplicativo criado a partir do modelo de serviço de trabalho especifica o SDK do trabalhador em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="089ef-119">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="089ef-120">Para usar o modelo como base para um aplicativo de serviços hospedados:</span><span class="sxs-lookup"><span data-stu-id="089ef-120">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="089ef-121">Pacote</span><span class="sxs-lookup"><span data-stu-id="089ef-121">Package</span></span>

<span data-ttu-id="089ef-122">Um aplicativo baseado no modelo de serviço de trabalho usa o `Microsoft.NET.Sdk.Worker` SDK e tem uma referência de pacote explícita para o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="089ef-122">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="089ef-123">Por exemplo, consulte o arquivo de projeto do aplicativo de exemplo (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="089ef-123">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="089ef-124">Para aplicativos Web que usam o `Microsoft.NET.Sdk.Web` SDK, o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) é referenciado implicitamente da estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="089ef-124">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="089ef-125">Uma referência de pacote explícita no arquivo de projeto do aplicativo não é necessária.</span><span class="sxs-lookup"><span data-stu-id="089ef-125">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="089ef-126">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="089ef-126">IHostedService interface</span></span>

<span data-ttu-id="089ef-127">A <xref:Microsoft.Extensions.Hosting.IHostedService> interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="089ef-127">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="089ef-128">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-128">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="089ef-129">`StartAsync`é chamado *antes*de:</span><span class="sxs-lookup"><span data-stu-id="089ef-129">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="089ef-130">O pipeline de processamento de solicitação do aplicativo está configurado ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="089ef-130">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="089ef-131">O servidor é iniciado e [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="089ef-131">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="089ef-132">O comportamento padrão pode ser alterado para que o serviço hospedado `StartAsync` seja executado depois que o pipeline do aplicativo tiver sido configurado e `ApplicationStarted` chamado.</span><span class="sxs-lookup"><span data-stu-id="089ef-132">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="089ef-133">Para alterar o comportamento padrão, adicione o serviço hospedado ( `VideosWatcher` no exemplo a seguir) após chamar `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="089ef-133">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="089ef-134">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="089ef-134">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="089ef-135">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-135">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="089ef-136">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="089ef-136">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="089ef-137">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="089ef-137">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="089ef-138">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="089ef-138">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="089ef-139">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="089ef-139">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="089ef-140">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="089ef-140">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="089ef-141">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="089ef-141">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="089ef-142">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="089ef-142">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="089ef-143">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="089ef-143">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="089ef-144">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="089ef-144">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="089ef-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="089ef-145"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="089ef-146">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="089ef-146">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="089ef-147">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="089ef-147">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="089ef-148">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="089ef-148">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="089ef-149">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="089ef-149">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="089ef-150">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="089ef-150">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="089ef-151">Classe base BackgroundService</span><span class="sxs-lookup"><span data-stu-id="089ef-151">BackgroundService base class</span></span>

<span data-ttu-id="089ef-152"><xref:Microsoft.Extensions.Hosting.BackgroundService>é uma classe base para implementar uma longa execução <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="089ef-152"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="089ef-153">[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) é chamado para executar o serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-153">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="089ef-154">A implementação retorna um <xref:System.Threading.Tasks.Task> que representa o tempo de vida inteiro do serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-154">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="089ef-155">Nenhum serviço adicional é iniciado até que [ExecuteAsync se torne assíncrono](https://github.com/dotnet/extensions/issues/2149), por exemplo, chamando `await` .</span><span class="sxs-lookup"><span data-stu-id="089ef-155">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="089ef-156">Evite executar tempo demorado, bloqueando o trabalho de inicialização no `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="089ef-156">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="089ef-157">Os blocos de host em [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) aguardando `ExecuteAsync` para serem concluídos.</span><span class="sxs-lookup"><span data-stu-id="089ef-157">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="089ef-158">O token de cancelamento é disparado quando [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) é chamado.</span><span class="sxs-lookup"><span data-stu-id="089ef-158">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="089ef-159">Sua implementação do `ExecuteAsync` deve ser concluída imediatamente quando o token de cancelamento é acionado para desligar o serviço normalmente.</span><span class="sxs-lookup"><span data-stu-id="089ef-159">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="089ef-160">Caso contrário, o serviço será desligado sem nenhum normal no tempo limite do desligamento.</span><span class="sxs-lookup"><span data-stu-id="089ef-160">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="089ef-161">Para obter mais informações, consulte a seção [interface IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="089ef-161">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="089ef-162">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="089ef-162">Timed background tasks</span></span>

<span data-ttu-id="089ef-163">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="089ef-163">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="089ef-164">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="089ef-164">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="089ef-165">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="089ef-165">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="089ef-166">O <xref:System.Threading.Timer> não aguarda as execuções anteriores do `DoWork` serem concluídas, portanto, a abordagem mostrada pode não ser adequada para cada cenário.</span><span class="sxs-lookup"><span data-stu-id="089ef-166">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="089ef-167">[Interlocked. Increment](xref:System.Threading.Interlocked.Increment*) é usado para incrementar o contador de execução como uma operação atômica, o que garante que vários threads não sejam atualizados `executionCount` simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="089ef-167">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="089ef-168">O serviço está registrado em `IHostBuilder.ConfigureServices` (*Program.cs*) com o `AddHostedService` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="089ef-168">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="089ef-169">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="089ef-169">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="089ef-170">Para usar [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) em um [BackgroundService](#backgroundservice-base-class), crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="089ef-170">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="089ef-171">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="089ef-171">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="089ef-172">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-172">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="089ef-173">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="089ef-173">In the following example:</span></span>

* <span data-ttu-id="089ef-174">O serviço é assíncrono.</span><span class="sxs-lookup"><span data-stu-id="089ef-174">The service is asynchronous.</span></span> <span data-ttu-id="089ef-175">O método `DoWork` retorna um `Task`.</span><span class="sxs-lookup"><span data-stu-id="089ef-175">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="089ef-176">Para fins de demonstração, um atraso de dez segundos é aguardado no `DoWork` método.</span><span class="sxs-lookup"><span data-stu-id="089ef-176">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="089ef-177">Um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço.</span><span class="sxs-lookup"><span data-stu-id="089ef-177">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="089ef-178">O serviço hospedado cria um escopo para resolver o serviço de tarefa em segundo plano com escopo para chamar seu `DoWork` método.</span><span class="sxs-lookup"><span data-stu-id="089ef-178">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="089ef-179">`DoWork`Retorna um `Task` , que é esperado em `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="089ef-179">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="089ef-180">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="089ef-180">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="089ef-181">O serviço hospedado está registrado com o `AddHostedService` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="089ef-181">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="089ef-182">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="089ef-182">Queued background tasks</span></span>

<span data-ttu-id="089ef-183">Uma fila de tarefas em segundo plano é baseada no .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="089ef-183">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="089ef-184">No exemplo a seguir `QueueHostedService` :</span><span class="sxs-lookup"><span data-stu-id="089ef-184">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="089ef-185">O `BackgroundProcessing` método retorna um `Task` , que é esperado em `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="089ef-185">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="089ef-186">As tarefas em segundo plano na fila são removidas da fila e executadas no `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="089ef-186">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="089ef-187">Os itens de trabalho são aguardados antes de o serviço parar `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="089ef-187">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="089ef-188">Um `MonitorLoop` serviço lida com tarefas de enfileiramento para o serviço hospedado sempre que a `w` chave é selecionada em um dispositivo de entrada:</span><span class="sxs-lookup"><span data-stu-id="089ef-188">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="089ef-189">O `IBackgroundTaskQueue` é injetado no `MonitorLoop` serviço.</span><span class="sxs-lookup"><span data-stu-id="089ef-189">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="089ef-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem`é chamado para enfileirar um item de trabalho.</span><span class="sxs-lookup"><span data-stu-id="089ef-190">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="089ef-191">O item de trabalho simula uma tarefa em segundo plano de execução longa:</span><span class="sxs-lookup"><span data-stu-id="089ef-191">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="089ef-192">Três atrasos de 5 segundos são executados ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="089ef-192">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="089ef-193">Uma `try-catch` instrução intercepta <xref:System.OperationCanceledException> se a tarefa é cancelada.</span><span class="sxs-lookup"><span data-stu-id="089ef-193">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="089ef-194">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="089ef-194">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="089ef-195">O serviço hospedado está registrado com o `AddHostedService` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="089ef-195">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="089ef-196">`MonitorLoop`é iniciado em `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="089ef-196">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="089ef-197">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="089ef-197">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="089ef-198">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="089ef-198">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="089ef-199">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="089ef-199">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="089ef-200">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="089ef-200">Background task that runs on a timer.</span></span>
* <span data-ttu-id="089ef-201">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="089ef-201">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="089ef-202">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="089ef-202">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="089ef-203">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="089ef-203">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="089ef-204">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="089ef-204">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="089ef-205">Pacote</span><span class="sxs-lookup"><span data-stu-id="089ef-205">Package</span></span>

<span data-ttu-id="089ef-206">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="089ef-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="089ef-207">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="089ef-207">IHostedService interface</span></span>

<span data-ttu-id="089ef-208">Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="089ef-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="089ef-209">A interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="089ef-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="089ef-210">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="089ef-211">Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="089ef-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="089ef-212">Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.</span><span class="sxs-lookup"><span data-stu-id="089ef-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="089ef-213">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="089ef-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="089ef-214">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="089ef-215">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="089ef-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="089ef-216">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="089ef-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="089ef-217">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="089ef-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="089ef-218">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="089ef-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="089ef-219">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="089ef-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="089ef-220">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="089ef-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="089ef-221">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="089ef-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="089ef-222">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="089ef-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="089ef-223">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="089ef-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="089ef-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="089ef-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="089ef-225">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="089ef-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="089ef-226">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="089ef-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="089ef-227">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="089ef-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="089ef-228">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="089ef-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="089ef-229">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="089ef-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="089ef-230">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="089ef-230">Timed background tasks</span></span>

<span data-ttu-id="089ef-231">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="089ef-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="089ef-232">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="089ef-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="089ef-233">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="089ef-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="089ef-234">O <xref:System.Threading.Timer> não aguarda as execuções anteriores do `DoWork` serem concluídas, portanto, a abordagem mostrada pode não ser adequada para cada cenário.</span><span class="sxs-lookup"><span data-stu-id="089ef-234">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="089ef-235">O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="089ef-235">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="089ef-236">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="089ef-236">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="089ef-237">Para usar [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `IHostedService` , crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="089ef-237">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="089ef-238">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="089ef-238">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="089ef-239">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="089ef-239">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="089ef-240">No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:</span><span class="sxs-lookup"><span data-stu-id="089ef-240">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="089ef-241">O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="089ef-241">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="089ef-242">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="089ef-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="089ef-243">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="089ef-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="089ef-244">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="089ef-244">Queued background tasks</span></span>

<span data-ttu-id="089ef-245">Uma fila de tarefas em segundo plano é baseada no .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="089ef-245">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="089ef-246">No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um [BackgroundService](#backgroundservice-base-class), que é uma classe base para implementar um `IHostedService` de longa execução:</span><span class="sxs-lookup"><span data-stu-id="089ef-246">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="089ef-247">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="089ef-247">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="089ef-248">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="089ef-248">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="089ef-249">Na classe de modelo de página de índice:</span><span class="sxs-lookup"><span data-stu-id="089ef-249">In the Index page model class:</span></span>

* <span data-ttu-id="089ef-250">O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="089ef-250">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="089ef-251">Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="089ef-251">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="089ef-252">O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo.</span><span class="sxs-lookup"><span data-stu-id="089ef-252">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="089ef-253">Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).</span><span class="sxs-lookup"><span data-stu-id="089ef-253">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="089ef-254">Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado.</span><span class="sxs-lookup"><span data-stu-id="089ef-254">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="089ef-255">`QueueBackgroundWorkItem`é chamado para enfileirar um item de trabalho:</span><span class="sxs-lookup"><span data-stu-id="089ef-255">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="089ef-256">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="089ef-256">Additional resources</span></span>

* [<span data-ttu-id="089ef-257">Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="089ef-257">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="089ef-258">Executar tarefas em segundo plano com trabalhos Web no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="089ef-258">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
