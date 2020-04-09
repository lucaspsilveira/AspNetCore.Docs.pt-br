---
title: Verificações de integridade no ASP.NET Core
author: rick-anderson
description: Saiba como configurar verificações de integridade para a infraestrutura do ASP.NET Core, como aplicativos e bancos de dados.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664881"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="10f45-103">Verificações de integridade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10f45-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="10f45-104">Por [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="10f45-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10f45-105">ASP.NET Core oferece middleware de verificação de saúde e bibliotecas para relatar a saúde dos componentes da infra-estrutura de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="10f45-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="10f45-106">As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="10f45-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="10f45-107">Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:</span><span class="sxs-lookup"><span data-stu-id="10f45-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="10f45-108">As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="10f45-109">Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner.</span><span class="sxs-lookup"><span data-stu-id="10f45-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="10f45-110">Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.</span><span class="sxs-lookup"><span data-stu-id="10f45-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="10f45-111">O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="10f45-112">As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.</span><span class="sxs-lookup"><span data-stu-id="10f45-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="10f45-113">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10f45-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="10f45-114">O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="10f45-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="10f45-115">Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="10f45-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="10f45-116">Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="10f45-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="10f45-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="10f45-117">Prerequisites</span></span>

<span data-ttu-id="10f45-118">As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="10f45-119">Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado.</span><span class="sxs-lookup"><span data-stu-id="10f45-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="10f45-120">O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="10f45-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="10f45-121">O pacote [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) é referenciado implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="10f45-122">Para realizar verificações de saúde usando o Entity Framework Core, adicione uma referência de pacote ao pacote [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore.](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="10f45-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="10f45-123">O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="10f45-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="10f45-124">O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="10f45-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="10f45-125">O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="10f45-126">Para explorar os cenários de banco de dados, o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="10f45-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="10f45-127">Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="10f45-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="10f45-128">Tem as seguintes referências de pacote em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="10f45-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="10f45-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="10f45-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="10f45-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="10f45-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="10f45-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="10f45-132">Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="10f45-133">O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="10f45-134">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="10f45-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="10f45-135">Investigação de integridade básica</span><span class="sxs-lookup"><span data-stu-id="10f45-135">Basic health probe</span></span>

<span data-ttu-id="10f45-136">Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="10f45-137">A configuração básica registra serviços de verificação de saúde e chama o Health Checks Middleware para responder em um ponto final de URL com uma resposta de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="10f45-138">Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico.</span><span class="sxs-lookup"><span data-stu-id="10f45-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="10f45-139">O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="10f45-140">O gravador de resposta padrão grava o status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como uma resposta de texto não criptografado no cliente, indicando um status [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="10f45-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="10f45-141">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-142">Crie um ponto final `MapHealthChecks` de `Startup.Configure`verificação de saúde ligando para dentro .</span><span class="sxs-lookup"><span data-stu-id="10f45-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="10f45-143">No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="10f45-144">Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="10f45-145">Exemplo do Docker</span><span class="sxs-lookup"><span data-stu-id="10f45-145">Docker example</span></span>

<span data-ttu-id="10f45-146">O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="10f45-147">Criar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-147">Create health checks</span></span>

<span data-ttu-id="10f45-148">As verificações de integridade são criadas pela implementação da interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="10f45-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="10f45-149">O método <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retorna um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="10f45-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="10f45-150">O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="10f45-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="10f45-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> também pode retornar pares chave-valor opcionais.</span><span class="sxs-lookup"><span data-stu-id="10f45-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="10f45-152">A `ExampleHealthCheck` classe a seguir demonstra o layout de um exame de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="10f45-153">A lógica de verificação `CheckHealthAsync` de saúde é colocada no método.</span><span class="sxs-lookup"><span data-stu-id="10f45-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="10f45-154">O exemplo a seguir define `healthCheckResultHealthy`uma `true`variável de boneco, para .</span><span class="sxs-lookup"><span data-stu-id="10f45-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="10f45-155">Se o `healthCheckResultHealthy` valor do `false`for definido como , o status [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) é devolvido.</span><span class="sxs-lookup"><span data-stu-id="10f45-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="10f45-156">Registrar os serviços de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-156">Register health check services</span></span>

<span data-ttu-id="10f45-157">O `ExampleHealthCheck` tipo é adicionado aos <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices`serviços de verificação de saúde com em :</span><span class="sxs-lookup"><span data-stu-id="10f45-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="10f45-158">A sobrecarga <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> mostrada no exemplo a seguir define o status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como relatório quando a verificação de integridade relata uma falha.</span><span class="sxs-lookup"><span data-stu-id="10f45-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="10f45-159">Se o status de falha for definido como `null` (padrão), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) será relatado.</span><span class="sxs-lookup"><span data-stu-id="10f45-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="10f45-160">Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.</span><span class="sxs-lookup"><span data-stu-id="10f45-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="10f45-161">*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="10f45-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="10f45-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> também pode executar uma função lambda.</span><span class="sxs-lookup"><span data-stu-id="10f45-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="10f45-163">No seguinte exemplo, o nome da verificação de integridade é especificado como `Example` e a verificação sempre retorna um estado íntegro:</span><span class="sxs-lookup"><span data-stu-id="10f45-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="10f45-164">Chamada <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> para passar argumentos para uma implementação de verificação de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="10f45-165">No exemplo a `TestHealthCheckWithArgs` seguir, aceita um inteiro e <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> uma seqüência de string para uso quando é chamado:</span><span class="sxs-lookup"><span data-stu-id="10f45-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="10f45-166">`TestHealthCheckWithArgs`é registrado ligando `AddTypeActivatedCheck` com o inteiro e a seqüência passou para a implementação:</span><span class="sxs-lookup"><span data-stu-id="10f45-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="10f45-167">Use o roteamento de verificações de saúde</span><span class="sxs-lookup"><span data-stu-id="10f45-167">Use Health Checks Routing</span></span>

<span data-ttu-id="10f45-168">Em `Startup.Configure`, `MapHealthChecks` chamar o construtor de ponto final com a URL de ponto final ou caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="10f45-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="10f45-169">Exigir host</span><span class="sxs-lookup"><span data-stu-id="10f45-169">Require host</span></span>

<span data-ttu-id="10f45-170">Ligue `RequireHost` para especificar um ou mais hosts permitidos para o ponto final do exame de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="10f45-171">Os hosts devem ser Unicode em vez de insignificantes e podem incluir uma porta.</span><span class="sxs-lookup"><span data-stu-id="10f45-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="10f45-172">Se uma coleção não for fornecida, qualquer hospedeiro é aceito.</span><span class="sxs-lookup"><span data-stu-id="10f45-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="10f45-173">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="10f45-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="10f45-174">Exigir autorização</span><span class="sxs-lookup"><span data-stu-id="10f45-174">Require authorization</span></span>

<span data-ttu-id="10f45-175">Chamada `RequireAuthorization` para executar O Middleware de autorização no ponto final da solicitação de verificação de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="10f45-176">Uma `RequireAuthorization` sobrecarga aceita uma ou mais políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="10f45-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="10f45-177">Se uma política não for fornecida, a política de autorização padrão será usada.</span><span class="sxs-lookup"><span data-stu-id="10f45-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="10f45-178">Habilitar o CORS (Solicitações Entre Origens)</span><span class="sxs-lookup"><span data-stu-id="10f45-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="10f45-179">Embora a realização de verificações de saúde manualmente a partir de um navegador `RequireCors` não seja um cenário de uso comum, o CORS Middleware pode ser habilitado solicitando pontos finais de verificação de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="10f45-180">Uma `RequireCors` sobrecarga aceita um delegado construtor`CorsPolicyBuilder`de políticas cors ( ) ou um nome de política.</span><span class="sxs-lookup"><span data-stu-id="10f45-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="10f45-181">Se uma diretiva não for fornecida, a diretiva CORS padrão será usada.</span><span class="sxs-lookup"><span data-stu-id="10f45-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="10f45-182">Para obter mais informações, consulte <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="10f45-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="10f45-183">Opções de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-183">Health check options</span></span>

<span data-ttu-id="10f45-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> oferece uma oportunidade de personalizar o comportamento de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="10f45-185">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="10f45-186">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="10f45-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="10f45-187">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="10f45-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="10f45-188">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="10f45-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="10f45-189">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-189">Filter health checks</span></span>

<span data-ttu-id="10f45-190">Por padrão, o Health Checks Middleware executa todos os cheques de saúde registrados.</span><span class="sxs-lookup"><span data-stu-id="10f45-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="10f45-191">Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="10f45-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="10f45-192">No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="10f45-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="10f45-193">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="10f45-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="10f45-194">Em `Startup.Configure`, `Predicate` os filtros para fora o 'Bar' verificação de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="10f45-195">Apenas Foo e Baz executam.</span><span class="sxs-lookup"><span data-stu-id="10f45-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="10f45-196">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="10f45-196">Customize the HTTP status code</span></span>

<span data-ttu-id="10f45-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> para personalizar o mapeamento de status da integridade para códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="10f45-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="10f45-198">As atribuições <xref:Microsoft.AspNetCore.Http.StatusCodes> a seguir são os valores padrão usados pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="10f45-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="10f45-199">Altere os valores do código de status de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="10f45-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="10f45-200">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="10f45-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="10f45-201">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="10f45-201">Suppress cache headers</span></span>

<span data-ttu-id="10f45-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>controla se o Health Checks Middleware adiciona cabeçalhos HTTP a uma resposta do teste para evitar o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="10f45-203">Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="10f45-204">Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="10f45-205">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="10f45-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="10f45-206">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="10f45-206">Customize output</span></span>

<span data-ttu-id="10f45-207">Em `Startup.Configure`, defina a opção [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) como um delegado para escrever a resposta:</span><span class="sxs-lookup"><span data-stu-id="10f45-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="10f45-208">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="10f45-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="10f45-209">Os seguintes delegados personalizados desmememotizem uma resposta JSON personalizada.</span><span class="sxs-lookup"><span data-stu-id="10f45-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="10f45-210">O primeiro exemplo do aplicativo de <xref:System.Text.Json?displayProperty=fullName>amostra demonstra como usar:</span><span class="sxs-lookup"><span data-stu-id="10f45-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="10f45-211">O segundo exemplo demonstra como usar [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="10f45-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="10f45-212">No aplicativo de exemplo, `SYSTEM_TEXT_JSON` comente a diretiva `Newtonsoft.Json` de `WriteResponse` [pré-processador](xref:index#preprocessor-directives-in-sample-code) em *CustomWriterStartup.cs* habilitar a versão de .</span><span class="sxs-lookup"><span data-stu-id="10f45-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="10f45-213">A API de verificações de saúde não fornece suporte integrado para formatos complexos de retorno JSON porque o formato é específico para a sua escolha do sistema de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="10f45-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="10f45-214">Personalize a resposta nos exemplos anteriores conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="10f45-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="10f45-215">Para obter mais informações sobre `System.Text.Json`a serialização json com , consulte [Como serializar e desserializar JSON em .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="10f45-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="10f45-216">Investigação de banco de dados</span><span class="sxs-lookup"><span data-stu-id="10f45-216">Database probe</span></span>

<span data-ttu-id="10f45-217">Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="10f45-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="10f45-218">O aplicativo de exemplo usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="10f45-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="10f45-219">O `AspNetCore.Diagnostics.HealthChecks` executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.</span><span class="sxs-lookup"><span data-stu-id="10f45-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="10f45-220">Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente.</span><span class="sxs-lookup"><span data-stu-id="10f45-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="10f45-221">A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="10f45-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="10f45-222">Na maioria dos casos, a execução de uma consulta de teste não é necessária.</span><span class="sxs-lookup"><span data-stu-id="10f45-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="10f45-223">É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="10f45-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="10f45-224">Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="10f45-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="10f45-225">Inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="10f45-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="10f45-226">Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="10f45-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="10f45-227">O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="10f45-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="10f45-228">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-229">O aplicativo de exemplo chama o método `AddSqlServer` com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="10f45-230">Um ponto final de verificação `MapHealthChecks` `Startup.Configure`de saúde é criado ligando para:</span><span class="sxs-lookup"><span data-stu-id="10f45-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="10f45-231">Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="10f45-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="10f45-233">Investigação de DbContext do Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="10f45-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="10f45-234">A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="10f45-235">A verificação `DbContext` é compatível em aplicativos que:</span><span class="sxs-lookup"><span data-stu-id="10f45-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="10f45-236">Usam o [EF (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="10f45-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="10f45-237">Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="10f45-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="10f45-238">`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="10f45-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="10f45-239">O `DbContext` é fornecido como o `TContext` para o método.</span><span class="sxs-lookup"><span data-stu-id="10f45-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="10f45-240">Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.</span><span class="sxs-lookup"><span data-stu-id="10f45-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="10f45-241">Por padrão:</span><span class="sxs-lookup"><span data-stu-id="10f45-241">By default:</span></span>

* <span data-ttu-id="10f45-242">o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="10f45-243">Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="10f45-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="10f45-244">O nome da verificação de integridade é o nome do tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="10f45-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="10f45-245">No aplicativo de `AppDbContext` amostra, `AddDbContextCheck` é fornecido e registrado `Startup.ConfigureServices` como um serviço em (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="10f45-246">Um ponto final de verificação `MapHealthChecks` `Startup.Configure`de saúde é criado ligando para:</span><span class="sxs-lookup"><span data-stu-id="10f45-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="10f45-247">Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="10f45-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="10f45-248">Se o banco de dados existir, exclua-o.</span><span class="sxs-lookup"><span data-stu-id="10f45-248">If the database exists, delete it.</span></span>

<span data-ttu-id="10f45-249">Execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="10f45-250">Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador.</span><span class="sxs-lookup"><span data-stu-id="10f45-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="10f45-251">O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="10f45-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="10f45-252">Dispare o aplicativo de exemplo para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="10f45-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="10f45-253">Faça uma solicitação para `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="10f45-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="10f45-254">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="10f45-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="10f45-255">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="10f45-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="10f45-256">O banco de dados e o contexto existem e, portanto, o aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="10f45-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="10f45-257">Dispare o aplicativo de exemplo para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="10f45-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="10f45-258">Faça uma solicitação para `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="10f45-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="10f45-259">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="10f45-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="10f45-260">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="10f45-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="10f45-261">O aplicativo fornece uma resposta não íntegra:</span><span class="sxs-lookup"><span data-stu-id="10f45-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="10f45-262">Investigações de preparação e atividade separadas</span><span class="sxs-lookup"><span data-stu-id="10f45-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="10f45-263">Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="10f45-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="10f45-264">O aplicativo está funcionando, mas ainda não está pronto para receber solicitações.</span><span class="sxs-lookup"><span data-stu-id="10f45-264">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="10f45-265">Esse estado é a *preparação* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-265">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="10f45-266">O aplicativo está funcionando e respondendo a solicitações.</span><span class="sxs-lookup"><span data-stu-id="10f45-266">The app is functioning and responding to requests.</span></span> <span data-ttu-id="10f45-267">Esse estado é a *atividade* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-267">This state is the app's *liveness*.</span></span>

<span data-ttu-id="10f45-268">A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="10f45-268">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="10f45-269">Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="10f45-269">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="10f45-270">Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="10f45-270">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="10f45-271">O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="10f45-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="10f45-272">A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="10f45-273">A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="10f45-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="10f45-274">Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:</span><span class="sxs-lookup"><span data-stu-id="10f45-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="10f45-275">A verificação de integridade é registrada em <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices` juntamente com o serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="10f45-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="10f45-276">Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="10f45-277">Um ponto final de verificação `MapHealthChecks` `Startup.Configure`de saúde é criado ligando para dentro .</span><span class="sxs-lookup"><span data-stu-id="10f45-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="10f45-278">No aplicativo de exemplo, os pontos finais da verificação de saúde são criados em:</span><span class="sxs-lookup"><span data-stu-id="10f45-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="10f45-279">`/health/ready`para a verificação de prontidão.</span><span class="sxs-lookup"><span data-stu-id="10f45-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="10f45-280">A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`.</span><span class="sxs-lookup"><span data-stu-id="10f45-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="10f45-281">`/health/live`para a verificação de vida.</span><span class="sxs-lookup"><span data-stu-id="10f45-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="10f45-282">A verificação de vida `StartupHostedServiceHealthCheck` filtra `false` o retornando no [HealthCheckOptions.Predicado](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (para obter mais informações, consulte [Filtro de verificação de saúde](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="10f45-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="10f45-283">No seguinte código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="10f45-283">In the following example code:</span></span>

* <span data-ttu-id="10f45-284">A verificação de prontidão usa todas as verificações registradas com a tag 'ready'.</span><span class="sxs-lookup"><span data-stu-id="10f45-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="10f45-285">O `Predicate` exclude todos os cheques e devolve um 200-Ok.</span><span class="sxs-lookup"><span data-stu-id="10f45-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="10f45-286">Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="10f45-287">Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="10f45-288">A verificação de integridade informa *Não íntegro* para os primeiros 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="10f45-289">Após 15 segundos, o ponto de extremidade informa *Íntegro*, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="10f45-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="10f45-290">Este exemplo também cria um Publicador de Verificação de Integridade (implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) que executa a primeira verificação de preparação com um atraso de dois segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="10f45-291">Para saber mais, confira a seção [Publicador de Verificação de Integridade](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="10f45-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="10f45-292">Exemplo do Kubernetes</span><span class="sxs-lookup"><span data-stu-id="10f45-292">Kubernetes example</span></span>

<span data-ttu-id="10f45-293">O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="10f45-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="10f45-294">No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="10f45-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="10f45-295">O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="10f45-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="10f45-296">Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="10f45-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="10f45-297">O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="10f45-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="10f45-298">Investigação baseada em métrica com um gravador de resposta personalizada</span><span class="sxs-lookup"><span data-stu-id="10f45-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="10f45-299">O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.</span><span class="sxs-lookup"><span data-stu-id="10f45-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="10f45-300">`MemoryHealthCheck` relata um estado degradado se o aplicativo usa mais de determinado limite de memória (1 GB no aplicativo de exemplo).</span><span class="sxs-lookup"><span data-stu-id="10f45-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="10f45-301">O <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="10f45-302">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-303">Em vez de permitir a verificação de integridade passando-a para <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, a `MemoryHealthCheck` é registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="10f45-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="10f45-304">Todos os serviços registrados da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> estão disponíveis para os serviços de verificação de integridade e middleware.</span><span class="sxs-lookup"><span data-stu-id="10f45-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="10f45-305">Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.</span><span class="sxs-lookup"><span data-stu-id="10f45-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="10f45-306">Em *CustomWriterStartup.cs* do aplicativo de amostra:</span><span class="sxs-lookup"><span data-stu-id="10f45-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="10f45-307">Um ponto final de verificação `MapHealthChecks` `Startup.Configure`de saúde é criado ligando para dentro .</span><span class="sxs-lookup"><span data-stu-id="10f45-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="10f45-308">Um `WriteResponse` delegado é fornecido ao <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> propriedade para produzir uma resposta JSON personalizada quando a verificação de saúde for executada:</span><span class="sxs-lookup"><span data-stu-id="10f45-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="10f45-309">O `WriteResponse` delegado formata o `CompositeHealthCheckResult` em um objeto JSON e produz saída JSON para a resposta da verificação de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="10f45-310">Para obter mais informações, consulte a seção [Personalizar saída.](#customize-output)</span><span class="sxs-lookup"><span data-stu-id="10f45-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="10f45-311">Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="10f45-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de investigação de atividade de valor máximo e armazenamento em disco.</span><span class="sxs-lookup"><span data-stu-id="10f45-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="10f45-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="10f45-314">Filtrar por porta</span><span class="sxs-lookup"><span data-stu-id="10f45-314">Filter by port</span></span>

<span data-ttu-id="10f45-315">Ligue `RequireHost` `MapHealthChecks` com um padrão de URL que especifica uma porta para restringir as solicitações de verificação de saúde à porta especificada.</span><span class="sxs-lookup"><span data-stu-id="10f45-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="10f45-316">Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="10f45-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="10f45-317">O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10f45-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="10f45-318">A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="10f45-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="10f45-319">Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="10f45-320">Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.</span><span class="sxs-lookup"><span data-stu-id="10f45-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="10f45-321">O seguinte *arquivo Properties/launchSettings.json* no aplicativo de amostra não está incluído nos arquivos de projeto do aplicativo de amostra e deve ser criado manualmente:</span><span class="sxs-lookup"><span data-stu-id="10f45-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="10f45-322">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-323">Crie um ponto final `MapHealthChecks` de `Startup.Configure`verificação de saúde ligando para dentro .</span><span class="sxs-lookup"><span data-stu-id="10f45-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="10f45-324">No aplicativo de exemplo, `RequireHost` uma chamada `Startup.Configure` para o ponto final especifica a porta de gerenciamento da configuração:</span><span class="sxs-lookup"><span data-stu-id="10f45-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="10f45-325">Os pontos finais são criados `Startup.Configure`no aplicativo de exemplo em .</span><span class="sxs-lookup"><span data-stu-id="10f45-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="10f45-326">No seguinte código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="10f45-326">In the following example code:</span></span>

* <span data-ttu-id="10f45-327">A verificação de prontidão usa todas as verificações registradas com a tag 'ready'.</span><span class="sxs-lookup"><span data-stu-id="10f45-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="10f45-328">O `Predicate` exclude todos os cheques e devolve um 200-Ok.</span><span class="sxs-lookup"><span data-stu-id="10f45-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="10f45-329">Você pode evitar criar o arquivo *launchSettings.json* no aplicativo de exemplo definindo a porta de gerenciamento explicitamente em código.</span><span class="sxs-lookup"><span data-stu-id="10f45-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="10f45-330">Em *Program.cs* <xref:Microsoft.Extensions.Hosting.HostBuilder> onde o é criado, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> adicione uma chamada e forneça o ponto final da porta de gerenciamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="10f45-331">Em `Configure` *ManagementPortStartup.cs,* especifique `RequireHost`a porta de gerenciamento com:</span><span class="sxs-lookup"><span data-stu-id="10f45-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="10f45-332">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="10f45-332">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="10f45-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="10f45-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="10f45-334">Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="10f45-335">Distribuir uma biblioteca de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-335">Distribute a health check library</span></span>

<span data-ttu-id="10f45-336">Para distribuir uma verificação de integridade como uma biblioteca:</span><span class="sxs-lookup"><span data-stu-id="10f45-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="10f45-337">Escreva uma verificação de integridade que implementa a interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> como uma classe autônoma.</span><span class="sxs-lookup"><span data-stu-id="10f45-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="10f45-338">A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="10f45-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="10f45-339">Na lógica de `CheckHealthAsync`verificação de saúde de:</span><span class="sxs-lookup"><span data-stu-id="10f45-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="10f45-340">`data1`e `data2` são usados no método para executar a lógica de verificação de saúde da sonda.</span><span class="sxs-lookup"><span data-stu-id="10f45-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="10f45-341">`AccessViolationException`é manipulado.</span><span class="sxs-lookup"><span data-stu-id="10f45-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="10f45-342">Quando <xref:System.AccessViolationException> ocorre um, o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> é <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> devolvido com o para permitir que os usuários configurem o estado de falha das verificações de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="10f45-343">Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="10f45-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="10f45-344">No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="10f45-345">A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="10f45-346">Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="10f45-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="10f45-347">No seguinte exemplo, o chamador especifica itens opcionais:</span><span class="sxs-lookup"><span data-stu-id="10f45-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="10f45-348">nome da verificação de integridade (`name`).</span><span class="sxs-lookup"><span data-stu-id="10f45-348">health check name (`name`).</span></span> <span data-ttu-id="10f45-349">Se `null`, `example_health_check` é usado.</span><span class="sxs-lookup"><span data-stu-id="10f45-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="10f45-350">ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).</span><span class="sxs-lookup"><span data-stu-id="10f45-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="10f45-351">ponto de dados de inteiro para a verificação de integridade (`data2`).</span><span class="sxs-lookup"><span data-stu-id="10f45-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="10f45-352">Se `null`, `1` é usado.</span><span class="sxs-lookup"><span data-stu-id="10f45-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="10f45-353">status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="10f45-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="10f45-354">O padrão é `null`.</span><span class="sxs-lookup"><span data-stu-id="10f45-354">The default is `null`.</span></span> <span data-ttu-id="10f45-355">Se `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) for informado devido a um status de falha.</span><span class="sxs-lookup"><span data-stu-id="10f45-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="10f45-356">marcas (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="10f45-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="10f45-357">Publicador de Verificação de Integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-357">Health Check Publisher</span></span>

<span data-ttu-id="10f45-358">Quando um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado.</span><span class="sxs-lookup"><span data-stu-id="10f45-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="10f45-359">Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="10f45-360">A interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> tem um único método:</span><span class="sxs-lookup"><span data-stu-id="10f45-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="10f45-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> permite que você defina:</span><span class="sxs-lookup"><span data-stu-id="10f45-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="10f45-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; O atraso inicial aplicado após o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aplicativo começar antes de executar instâncias.</span><span class="sxs-lookup"><span data-stu-id="10f45-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="10f45-363">O atraso é aplicado uma vez na inicialização e não ocorre em iterações subsequentes.</span><span class="sxs-lookup"><span data-stu-id="10f45-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="10f45-364">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-364">The default value is five seconds.</span></span>
* <span data-ttu-id="10f45-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; O período <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> de execução.</span><span class="sxs-lookup"><span data-stu-id="10f45-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="10f45-366">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="10f45-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` for (padrão), o serviço de editor de cheques de saúde executa todos os cheques de saúde registrados.</span><span class="sxs-lookup"><span data-stu-id="10f45-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="10f45-368">Para executar um subconjunto das verificações de integridade, forneça uma função que filtre o conjunto de verificações.</span><span class="sxs-lookup"><span data-stu-id="10f45-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="10f45-369">O predicado é avaliado a cada período.</span><span class="sxs-lookup"><span data-stu-id="10f45-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="10f45-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; O tempo para a execução <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dos exames de saúde para todas as instâncias.</span><span class="sxs-lookup"><span data-stu-id="10f45-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="10f45-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> para executar sem um tempo limite.</span><span class="sxs-lookup"><span data-stu-id="10f45-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="10f45-372">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="10f45-373">No aplicativo de exemplo, `ReadinessPublisher` é uma implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="10f45-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="10f45-374">O status da verificação de saúde é registrado para cada verificação em um nível de registro de:</span><span class="sxs-lookup"><span data-stu-id="10f45-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="10f45-375">Informações<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) se o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>estado de verificação de saúde é .</span><span class="sxs-lookup"><span data-stu-id="10f45-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="10f45-376">Erro<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>status é ou .</span><span class="sxs-lookup"><span data-stu-id="10f45-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="10f45-377">No exemplo `LivenessProbeStartup` do aplicativo de amostra, a verificação de preparação `StartupHostedService` tem um atraso de inicialização de dois segundos, e executa a verificação a cada 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="10f45-378">Para ativar a implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, o exemplo registra `ReadinessPublisher` como um serviço singleton no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="10f45-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="10f45-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui publicadores para vários sistemas, incluindo [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="10f45-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="10f45-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="10f45-381">Restringir verificações de integridade com MapWhen</span><span class="sxs-lookup"><span data-stu-id="10f45-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="10f45-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> para ramificar condicionalmente o pipeline de solicitação para pontos de extremidade de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="10f45-383">No exemplo a `MapWhen` seguir, agências do pipeline de solicitação para ativar `api/HealthCheck` o Health Checks Middleware se uma solicitação GET for recebida para o ponto final:</span><span class="sxs-lookup"><span data-stu-id="10f45-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="10f45-384">Para obter mais informações, consulte <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="10f45-384">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10f45-385">ASP.NET Core oferece middleware de verificação de saúde e bibliotecas para relatar a saúde dos componentes da infra-estrutura de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="10f45-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="10f45-386">As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="10f45-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="10f45-387">Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:</span><span class="sxs-lookup"><span data-stu-id="10f45-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="10f45-388">As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="10f45-389">Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner.</span><span class="sxs-lookup"><span data-stu-id="10f45-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="10f45-390">Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.</span><span class="sxs-lookup"><span data-stu-id="10f45-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="10f45-391">O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="10f45-392">As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.</span><span class="sxs-lookup"><span data-stu-id="10f45-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="10f45-393">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10f45-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="10f45-394">O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="10f45-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="10f45-395">Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="10f45-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="10f45-396">Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="10f45-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="10f45-397">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="10f45-397">Prerequisites</span></span>

<span data-ttu-id="10f45-398">As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="10f45-399">Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado.</span><span class="sxs-lookup"><span data-stu-id="10f45-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="10f45-400">O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="10f45-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="10f45-401">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="10f45-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="10f45-402">O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="10f45-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="10f45-403">O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="10f45-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="10f45-404">O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="10f45-405">Para explorar os cenários de banco de dados, o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="10f45-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="10f45-406">Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="10f45-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="10f45-407">Tem as seguintes referências de pacote em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="10f45-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="10f45-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="10f45-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="10f45-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="10f45-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="10f45-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="10f45-411">Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="10f45-412">O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="10f45-413">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="10f45-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="10f45-414">Investigação de integridade básica</span><span class="sxs-lookup"><span data-stu-id="10f45-414">Basic health probe</span></span>

<span data-ttu-id="10f45-415">Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="10f45-416">A configuração básica registra serviços de verificação de saúde e chama o Health Checks Middleware para responder em um ponto final de URL com uma resposta de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="10f45-417">Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico.</span><span class="sxs-lookup"><span data-stu-id="10f45-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="10f45-418">O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="10f45-419">O gravador de resposta padrão grava o status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como uma resposta de texto não criptografado no cliente, indicando um status [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="10f45-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="10f45-420">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-421">Adicione um ponto final para <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> o Health Checks Middleware com no pipeline de processamento de solicitação de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="10f45-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="10f45-422">No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="10f45-423">Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="10f45-424">Exemplo do Docker</span><span class="sxs-lookup"><span data-stu-id="10f45-424">Docker example</span></span>

<span data-ttu-id="10f45-425">O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="10f45-426">Criar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-426">Create health checks</span></span>

<span data-ttu-id="10f45-427">As verificações de integridade são criadas pela implementação da interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="10f45-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="10f45-428">O método <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retorna um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="10f45-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="10f45-429">O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="10f45-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="10f45-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> também pode retornar pares chave-valor opcionais.</span><span class="sxs-lookup"><span data-stu-id="10f45-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="10f45-431">Verificação de integridade de exemplo</span><span class="sxs-lookup"><span data-stu-id="10f45-431">Example health check</span></span>

<span data-ttu-id="10f45-432">A `ExampleHealthCheck` classe a seguir demonstra o layout de um exame de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="10f45-433">A lógica de verificação `CheckHealthAsync` de saúde é colocada no método.</span><span class="sxs-lookup"><span data-stu-id="10f45-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="10f45-434">O exemplo a seguir define `healthCheckResultHealthy`uma `true`variável de boneco, para .</span><span class="sxs-lookup"><span data-stu-id="10f45-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="10f45-435">Se o `healthCheckResultHealthy` valor do `false`for definido como , o status [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) é devolvido.</span><span class="sxs-lookup"><span data-stu-id="10f45-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="10f45-436">Registrar os serviços de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-436">Register health check services</span></span>

<span data-ttu-id="10f45-437">O `ExampleHealthCheck` tipo é adicionado aos `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>serviços de verificação de saúde com:</span><span class="sxs-lookup"><span data-stu-id="10f45-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="10f45-438">A sobrecarga <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> mostrada no exemplo a seguir define o status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como relatório quando a verificação de integridade relata uma falha.</span><span class="sxs-lookup"><span data-stu-id="10f45-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="10f45-439">Se o status de falha for definido como `null` (padrão), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) será relatado.</span><span class="sxs-lookup"><span data-stu-id="10f45-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="10f45-440">Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.</span><span class="sxs-lookup"><span data-stu-id="10f45-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="10f45-441">*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="10f45-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="10f45-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> também pode executar uma função lambda.</span><span class="sxs-lookup"><span data-stu-id="10f45-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="10f45-443">No exemplo `Startup.ConfigureServices` a seguir, o nome `Example` da verificação de saúde é especificado como e o cheque sempre retorna um estado saudável:</span><span class="sxs-lookup"><span data-stu-id="10f45-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="10f45-444">Usar o Middleware de Verificações de Integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="10f45-445">Em `Startup.Configure`, chame <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> do pipeline de processamento com a URL de ponto de extremidade ou o caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="10f45-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="10f45-446">Se as verificações de integridade precisarem escutar uma porta específica, use uma sobrecarga igual a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> para definir a porta (descrito posteriormente na seção [Filtrar por porta](#filter-by-port)):</span><span class="sxs-lookup"><span data-stu-id="10f45-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="10f45-447">Opções de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-447">Health check options</span></span>

<span data-ttu-id="10f45-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> oferece uma oportunidade de personalizar o comportamento de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="10f45-449">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="10f45-450">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="10f45-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="10f45-451">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="10f45-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="10f45-452">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="10f45-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="10f45-453">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-453">Filter health checks</span></span>

<span data-ttu-id="10f45-454">Por padrão, o Health Checks Middleware executa todos os cheques de saúde registrados.</span><span class="sxs-lookup"><span data-stu-id="10f45-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="10f45-455">Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="10f45-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="10f45-456">No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="10f45-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="10f45-457">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="10f45-457">Customize the HTTP status code</span></span>

<span data-ttu-id="10f45-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> para personalizar o mapeamento de status da integridade para códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="10f45-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="10f45-459">As atribuições <xref:Microsoft.AspNetCore.Http.StatusCodes> a seguir são os valores padrão usados pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="10f45-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="10f45-460">Altere os valores do código de status de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="10f45-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="10f45-461">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="10f45-461">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="10f45-462">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="10f45-462">Suppress cache headers</span></span>

<span data-ttu-id="10f45-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>controla se o Health Checks Middleware adiciona cabeçalhos HTTP a uma resposta do teste para evitar o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="10f45-464">Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="10f45-465">Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="10f45-466">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="10f45-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="10f45-467">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="10f45-467">Customize output</span></span>

<span data-ttu-id="10f45-468">A opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtém ou define um representante usado para gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="10f45-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="10f45-469">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="10f45-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="10f45-470">Em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="10f45-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="10f45-471">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="10f45-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="10f45-472">O seguinte delegado `WriteResponse`personalizado, produz uma resposta JSON personalizada:</span><span class="sxs-lookup"><span data-stu-id="10f45-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="10f45-473">O sistema de verificação de saúde não fornece suporte integrado para formatos complexos de retorno JSON porque o formato é específico para a sua escolha do sistema de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="10f45-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="10f45-474">Sinta-se livre `JObject` para personalizar o exemplo anterior conforme necessário para atender às suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="10f45-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="10f45-475">Investigação de banco de dados</span><span class="sxs-lookup"><span data-stu-id="10f45-475">Database probe</span></span>

<span data-ttu-id="10f45-476">Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="10f45-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="10f45-477">O aplicativo de exemplo usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="10f45-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="10f45-478">O `AspNetCore.Diagnostics.HealthChecks` executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.</span><span class="sxs-lookup"><span data-stu-id="10f45-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="10f45-479">Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente.</span><span class="sxs-lookup"><span data-stu-id="10f45-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="10f45-480">A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="10f45-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="10f45-481">Na maioria dos casos, a execução de uma consulta de teste não é necessária.</span><span class="sxs-lookup"><span data-stu-id="10f45-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="10f45-482">É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="10f45-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="10f45-483">Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="10f45-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="10f45-484">Inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="10f45-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="10f45-485">Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="10f45-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="10f45-486">O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="10f45-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="10f45-487">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-488">O aplicativo de exemplo chama o método `AddSqlServer` com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="10f45-489">Ligue para a Health Checks Middleware `Startup.Configure`no pipeline de processamento de aplicativos em :</span><span class="sxs-lookup"><span data-stu-id="10f45-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="10f45-490">Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="10f45-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="10f45-492">Investigação de DbContext do Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="10f45-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="10f45-493">A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="10f45-494">A verificação `DbContext` é compatível em aplicativos que:</span><span class="sxs-lookup"><span data-stu-id="10f45-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="10f45-495">Usam o [EF (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="10f45-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="10f45-496">Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="10f45-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="10f45-497">`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="10f45-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="10f45-498">O `DbContext` é fornecido como o `TContext` para o método.</span><span class="sxs-lookup"><span data-stu-id="10f45-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="10f45-499">Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.</span><span class="sxs-lookup"><span data-stu-id="10f45-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="10f45-500">Por padrão:</span><span class="sxs-lookup"><span data-stu-id="10f45-500">By default:</span></span>

* <span data-ttu-id="10f45-501">o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="10f45-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="10f45-502">Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="10f45-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="10f45-503">O nome da verificação de integridade é o nome do tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="10f45-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="10f45-504">No aplicativo de `AppDbContext` amostra, `AddDbContextCheck` é fornecido e registrado `Startup.ConfigureServices` como um serviço em (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="10f45-505">No aplicativo de `UseHealthChecks` exemplo, adiciona o `Startup.Configure`Health Checks Middleware em .</span><span class="sxs-lookup"><span data-stu-id="10f45-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="10f45-506">Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="10f45-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="10f45-507">Se o banco de dados existir, exclua-o.</span><span class="sxs-lookup"><span data-stu-id="10f45-507">If the database exists, delete it.</span></span>

<span data-ttu-id="10f45-508">Execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="10f45-509">Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador.</span><span class="sxs-lookup"><span data-stu-id="10f45-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="10f45-510">O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="10f45-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="10f45-511">Dispare o aplicativo de exemplo para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="10f45-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="10f45-512">Faça uma solicitação para `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="10f45-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="10f45-513">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="10f45-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="10f45-514">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="10f45-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="10f45-515">O banco de dados e o contexto existem e, portanto, o aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="10f45-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="10f45-516">Dispare o aplicativo de exemplo para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="10f45-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="10f45-517">Faça uma solicitação para `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="10f45-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="10f45-518">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="10f45-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="10f45-519">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="10f45-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="10f45-520">O aplicativo fornece uma resposta não íntegra:</span><span class="sxs-lookup"><span data-stu-id="10f45-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="10f45-521">Investigações de preparação e atividade separadas</span><span class="sxs-lookup"><span data-stu-id="10f45-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="10f45-522">Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="10f45-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="10f45-523">O aplicativo está funcionando, mas ainda não está pronto para receber solicitações.</span><span class="sxs-lookup"><span data-stu-id="10f45-523">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="10f45-524">Esse estado é a *preparação* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-524">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="10f45-525">O aplicativo está funcionando e respondendo a solicitações.</span><span class="sxs-lookup"><span data-stu-id="10f45-525">The app is functioning and responding to requests.</span></span> <span data-ttu-id="10f45-526">Esse estado é a *atividade* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-526">This state is the app's *liveness*.</span></span>

<span data-ttu-id="10f45-527">A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="10f45-527">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="10f45-528">Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="10f45-528">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="10f45-529">Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="10f45-529">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="10f45-530">O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="10f45-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="10f45-531">A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="10f45-532">A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="10f45-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="10f45-533">Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:</span><span class="sxs-lookup"><span data-stu-id="10f45-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="10f45-534">A verificação de integridade é registrada em <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices` juntamente com o serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="10f45-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="10f45-535">Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="10f45-536">Ligue para a Health Checks Middleware `Startup.Configure`no pipeline de processamento de aplicativos em .</span><span class="sxs-lookup"><span data-stu-id="10f45-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="10f45-537">No aplicativo de exemplo, os pontos de extremidade de verificação de integridade são criados em `/health/ready` para a verificação de preparação e em `/health/live` para a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="10f45-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="10f45-538">A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`.</span><span class="sxs-lookup"><span data-stu-id="10f45-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="10f45-539">A verificação de atividade filtra a `StartupHostedServiceHealthCheck` retornando `false` no [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (para obter mais informações, confira [Filtrar verificações de integridade](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="10f45-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="10f45-540">Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="10f45-541">Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="10f45-542">A verificação de integridade informa *Não íntegro* para os primeiros 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="10f45-543">Após 15 segundos, o ponto de extremidade informa *Íntegro*, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="10f45-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="10f45-544">Este exemplo também cria um Publicador de Verificação de Integridade (implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) que executa a primeira verificação de preparação com um atraso de dois segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="10f45-545">Para saber mais, confira a seção [Publicador de Verificação de Integridade](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="10f45-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="10f45-546">Exemplo do Kubernetes</span><span class="sxs-lookup"><span data-stu-id="10f45-546">Kubernetes example</span></span>

<span data-ttu-id="10f45-547">O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="10f45-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="10f45-548">No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="10f45-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="10f45-549">O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="10f45-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="10f45-550">Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="10f45-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="10f45-551">O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="10f45-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="10f45-552">Investigação baseada em métrica com um gravador de resposta personalizada</span><span class="sxs-lookup"><span data-stu-id="10f45-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="10f45-553">O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.</span><span class="sxs-lookup"><span data-stu-id="10f45-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="10f45-554">`MemoryHealthCheck`relata um status insalubre se o aplicativo usar mais de um determinado limite de memória (1 GB no aplicativo de amostra).</span><span class="sxs-lookup"><span data-stu-id="10f45-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="10f45-555">O <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="10f45-556">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-557">Em vez de permitir a verificação de integridade passando-a para <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, a `MemoryHealthCheck` é registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="10f45-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="10f45-558">Todos os serviços registrados da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> estão disponíveis para os serviços de verificação de integridade e middleware.</span><span class="sxs-lookup"><span data-stu-id="10f45-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="10f45-559">Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.</span><span class="sxs-lookup"><span data-stu-id="10f45-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="10f45-560">No aplicativo de amostragem *(CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="10f45-561">Ligue para a Health Checks Middleware `Startup.Configure`no pipeline de processamento de aplicativos em .</span><span class="sxs-lookup"><span data-stu-id="10f45-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="10f45-562">Um representante `WriteResponse` é fornecido para a propriedade `ResponseWriter` para gerar uma resposta JSON personalizada quando a verificação de integridade é executada:</span><span class="sxs-lookup"><span data-stu-id="10f45-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="10f45-563">O método `WriteResponse` formata o `CompositeHealthCheckResult` em um objeto JSON e produz a saída JSON para a resposta da verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="10f45-564">Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="10f45-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de investigação de atividade de valor máximo e armazenamento em disco.</span><span class="sxs-lookup"><span data-stu-id="10f45-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="10f45-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="10f45-567">Filtrar por porta</span><span class="sxs-lookup"><span data-stu-id="10f45-567">Filter by port</span></span>

<span data-ttu-id="10f45-568">A chamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> com uma porta restringe as solicitações de verificação de integridade à porta especificada.</span><span class="sxs-lookup"><span data-stu-id="10f45-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="10f45-569">Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="10f45-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="10f45-570">O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10f45-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="10f45-571">A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="10f45-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="10f45-572">Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="10f45-573">Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.</span><span class="sxs-lookup"><span data-stu-id="10f45-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="10f45-574">O seguinte *arquivo Properties/launchSettings.json* no aplicativo de amostra não está incluído nos arquivos de projeto do aplicativo de amostra e deve ser criado manualmente:</span><span class="sxs-lookup"><span data-stu-id="10f45-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="10f45-575">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10f45-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10f45-576">A chamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> especifica a porta de gerenciamento (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="10f45-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="10f45-577">Evite a criação do arquivo *launchSettings.json* no aplicativo de exemplo definindo as URLs e a porta de gerenciamento explicitamente no código.</span><span class="sxs-lookup"><span data-stu-id="10f45-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="10f45-578">Em *Program.cs*, em que o <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> é criado, adicione uma chamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> e forneça o ponto de extremidade da resposta normal do aplicativo e o ponto de extremidade da porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="10f45-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="10f45-579">Em *ManagementPortStartup.cs*, em que <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> é chamado, especifique a porta de gerenciamento explicitamente.</span><span class="sxs-lookup"><span data-stu-id="10f45-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="10f45-580">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="10f45-580">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="10f45-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="10f45-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="10f45-582">Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="10f45-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="10f45-583">Distribuir uma biblioteca de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-583">Distribute a health check library</span></span>

<span data-ttu-id="10f45-584">Para distribuir uma verificação de integridade como uma biblioteca:</span><span class="sxs-lookup"><span data-stu-id="10f45-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="10f45-585">Escreva uma verificação de integridade que implementa a interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> como uma classe autônoma.</span><span class="sxs-lookup"><span data-stu-id="10f45-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="10f45-586">A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="10f45-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="10f45-587">Na lógica de `CheckHealthAsync`verificação de saúde de:</span><span class="sxs-lookup"><span data-stu-id="10f45-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="10f45-588">`data1`e `data2` são usados no método para executar a lógica de verificação de saúde da sonda.</span><span class="sxs-lookup"><span data-stu-id="10f45-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="10f45-589">`AccessViolationException`é manipulado.</span><span class="sxs-lookup"><span data-stu-id="10f45-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="10f45-590">Quando <xref:System.AccessViolationException> ocorre um, o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> é <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> devolvido com o para permitir que os usuários configurem o estado de falha das verificações de saúde.</span><span class="sxs-lookup"><span data-stu-id="10f45-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="10f45-591">Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="10f45-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="10f45-592">No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="10f45-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="10f45-593">A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="10f45-594">Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="10f45-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="10f45-595">No seguinte exemplo, o chamador especifica itens opcionais:</span><span class="sxs-lookup"><span data-stu-id="10f45-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="10f45-596">nome da verificação de integridade (`name`).</span><span class="sxs-lookup"><span data-stu-id="10f45-596">health check name (`name`).</span></span> <span data-ttu-id="10f45-597">Se `null`, `example_health_check` é usado.</span><span class="sxs-lookup"><span data-stu-id="10f45-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="10f45-598">ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).</span><span class="sxs-lookup"><span data-stu-id="10f45-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="10f45-599">ponto de dados de inteiro para a verificação de integridade (`data2`).</span><span class="sxs-lookup"><span data-stu-id="10f45-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="10f45-600">Se `null`, `1` é usado.</span><span class="sxs-lookup"><span data-stu-id="10f45-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="10f45-601">status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="10f45-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="10f45-602">O padrão é `null`.</span><span class="sxs-lookup"><span data-stu-id="10f45-602">The default is `null`.</span></span> <span data-ttu-id="10f45-603">Se `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) for informado devido a um status de falha.</span><span class="sxs-lookup"><span data-stu-id="10f45-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="10f45-604">marcas (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="10f45-604">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="10f45-605">Publicador de Verificação de Integridade</span><span class="sxs-lookup"><span data-stu-id="10f45-605">Health Check Publisher</span></span>

<span data-ttu-id="10f45-606">Quando um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado.</span><span class="sxs-lookup"><span data-stu-id="10f45-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="10f45-607">Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="10f45-608">A interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> tem um único método:</span><span class="sxs-lookup"><span data-stu-id="10f45-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="10f45-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> permite que você defina:</span><span class="sxs-lookup"><span data-stu-id="10f45-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="10f45-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; O atraso inicial aplicado após o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aplicativo começar antes de executar instâncias.</span><span class="sxs-lookup"><span data-stu-id="10f45-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="10f45-611">O atraso é aplicado uma vez na inicialização e não ocorre em iterações subsequentes.</span><span class="sxs-lookup"><span data-stu-id="10f45-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="10f45-612">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-612">The default value is five seconds.</span></span>
* <span data-ttu-id="10f45-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; O período <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> de execução.</span><span class="sxs-lookup"><span data-stu-id="10f45-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="10f45-614">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="10f45-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` for (padrão), o serviço de editor de cheques de saúde executa todos os cheques de saúde registrados.</span><span class="sxs-lookup"><span data-stu-id="10f45-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="10f45-616">Para executar um subconjunto das verificações de integridade, forneça uma função que filtre o conjunto de verificações.</span><span class="sxs-lookup"><span data-stu-id="10f45-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="10f45-617">O predicado é avaliado a cada período.</span><span class="sxs-lookup"><span data-stu-id="10f45-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="10f45-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; O tempo para a execução <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dos exames de saúde para todas as instâncias.</span><span class="sxs-lookup"><span data-stu-id="10f45-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="10f45-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> para executar sem um tempo limite.</span><span class="sxs-lookup"><span data-stu-id="10f45-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="10f45-620">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="10f45-621">Na versão para ASP.NET Core 2.2, a configuração de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> não é respeitada pela implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>; ela define o valor de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="10f45-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="10f45-622">Esta questão foi abordada em ASP.NET Núcleo 3.0.</span><span class="sxs-lookup"><span data-stu-id="10f45-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="10f45-623">No aplicativo de exemplo, `ReadinessPublisher` é uma implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="10f45-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="10f45-624">O status da verificação de saúde é registrado para cada verificação como:</span><span class="sxs-lookup"><span data-stu-id="10f45-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="10f45-625">Informações<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) se o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>estado de verificação de saúde é .</span><span class="sxs-lookup"><span data-stu-id="10f45-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="10f45-626">Erro<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ) se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> o <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>status é ou .</span><span class="sxs-lookup"><span data-stu-id="10f45-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="10f45-627">No exemplo `LivenessProbeStartup` do aplicativo de amostra, a verificação de preparação `StartupHostedService` tem um atraso de inicialização de dois segundos, e executa a verificação a cada 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="10f45-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="10f45-628">Para ativar a implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, o exemplo registra `ReadinessPublisher` como um serviço singleton no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="10f45-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="10f45-629">A solução alternativa a seguir permite a adição de uma instância <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ao contêiner de serviço quando um ou mais serviços hospedados já tiverem sido adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="10f45-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="10f45-630">Esta solução não é necessária em ASP.NET Núcleo 3.0.</span><span class="sxs-lookup"><span data-stu-id="10f45-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="10f45-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui publicadores para vários sistemas, incluindo [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="10f45-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="10f45-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou suportado pela Microsoft.</span><span class="sxs-lookup"><span data-stu-id="10f45-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="10f45-633">Restringir verificações de integridade com MapWhen</span><span class="sxs-lookup"><span data-stu-id="10f45-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="10f45-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> para ramificar condicionalmente o pipeline de solicitação para pontos de extremidade de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="10f45-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="10f45-635">No exemplo a `MapWhen` seguir, agências do pipeline de solicitação para ativar `api/HealthCheck` o Health Checks Middleware se uma solicitação GET for recebida para o ponto final:</span><span class="sxs-lookup"><span data-stu-id="10f45-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="10f45-636">Para obter mais informações, consulte <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="10f45-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
