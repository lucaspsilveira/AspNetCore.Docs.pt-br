---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488768"
---
# <a name="net-generic-host"></a><span data-ttu-id="498d7-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="498d7-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="498d7-104">Os modelos ASP.NET Core criam um <xref:Microsoft.Extensions.Hosting.HostBuilder>host genérico .NET Core, .</span><span class="sxs-lookup"><span data-stu-id="498d7-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="498d7-105">Definição do host</span><span class="sxs-lookup"><span data-stu-id="498d7-105">Host definition</span></span>

<span data-ttu-id="498d7-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="498d7-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="498d7-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="498d7-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="498d7-108">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="498d7-108">Logging</span></span>
* <span data-ttu-id="498d7-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="498d7-109">Configuration</span></span>
* <span data-ttu-id="498d7-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="498d7-110">`IHostedService` implementations</span></span>

<span data-ttu-id="498d7-111">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="498d7-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="498d7-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="498d7-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="498d7-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="498d7-114">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="498d7-114">Set up a host</span></span>

<span data-ttu-id="498d7-115">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="498d7-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="498d7-116">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="498d7-116">The `Main` method:</span></span>

* <span data-ttu-id="498d7-117">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="498d7-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="498d7-118">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="498d7-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="498d7-119">Os modelos da Web ASP.NET Core geram o seguinte código para criar um Host genérico:</span><span class="sxs-lookup"><span data-stu-id="498d7-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="498d7-120">O código a seguir cria um Host genérico usando carga de trabalho não-HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="498d7-121">A `IHostedService` implementação é adicionada ao contêiner DI:</span><span class="sxs-lookup"><span data-stu-id="498d7-121">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="498d7-122">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="498d7-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="498d7-123">O código anterior é gerado pelos modelos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="498d7-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="498d7-124">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="498d7-125">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="498d7-126">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="498d7-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="498d7-127">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="498d7-127">Default builder settings</span></span>

<span data-ttu-id="498d7-128">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="498d7-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="498d7-129">Define a raiz de [conteúdo](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>para o caminho retornado por .</span><span class="sxs-lookup"><span data-stu-id="498d7-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="498d7-130">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="498d7-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="498d7-131">Variáveis de ambiente `DOTNET_`prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="498d7-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="498d7-132">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="498d7-132">Command-line arguments.</span></span>
* <span data-ttu-id="498d7-133">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="498d7-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="498d7-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="498d7-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="498d7-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="498d7-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="498d7-136">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="498d7-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="498d7-137">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="498d7-137">Environment variables.</span></span>
  * <span data-ttu-id="498d7-138">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="498d7-138">Command-line arguments.</span></span>
* <span data-ttu-id="498d7-139">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="498d7-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="498d7-140">Console</span><span class="sxs-lookup"><span data-stu-id="498d7-140">Console</span></span>
  * <span data-ttu-id="498d7-141">Depurar</span><span class="sxs-lookup"><span data-stu-id="498d7-141">Debug</span></span>
  * <span data-ttu-id="498d7-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="498d7-142">EventSource</span></span>
  * <span data-ttu-id="498d7-143">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="498d7-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="498d7-144">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="498d7-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="498d7-145">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="498d7-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="498d7-146">Carrega a configuração do host `ASPNETCORE_`a partir de variáveis de ambiente prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="498d7-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="498d7-147">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="498d7-148">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="498d7-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="498d7-149">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="498d7-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="498d7-150">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true`se for igual .</span><span class="sxs-lookup"><span data-stu-id="498d7-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="498d7-151">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="498d7-151">Enables IIS integration.</span></span> <span data-ttu-id="498d7-152">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="498d7-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="498d7-153">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="498d7-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="498d7-154">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="498d7-154">Framework-provided services</span></span>

<span data-ttu-id="498d7-155">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="498d7-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="498d7-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="498d7-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="498d7-158">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="498d7-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="498d7-159">Para obter mais informações sobre serviços fornecidos por estruturas, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="498d7-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="498d7-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="498d7-161">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="498d7-162">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="498d7-163">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="498d7-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="498d7-164">O exemplo a `IHostedService` seguir é `IHostApplicationLifetime` uma implementação que registra eventos:</span><span class="sxs-lookup"><span data-stu-id="498d7-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="498d7-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-165">IHostLifetime</span></span>

<span data-ttu-id="498d7-166">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="498d7-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="498d7-167">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="498d7-167">The last implementation registered is used.</span></span>

<span data-ttu-id="498d7-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="498d7-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="498d7-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="498d7-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="498d7-170">Ouve <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="498d7-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="498d7-171">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="498d7-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="498d7-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="498d7-172">IHostEnvironment</span></span>

<span data-ttu-id="498d7-173">Injete o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="498d7-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="498d7-174">Applicationname</span><span class="sxs-lookup"><span data-stu-id="498d7-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="498d7-175">Nome do ambiente</span><span class="sxs-lookup"><span data-stu-id="498d7-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="498d7-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="498d7-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="498d7-177">Os aplicativos `IWebHostEnvironment` da Web implementam a interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="498d7-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="498d7-178">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="498d7-178">Host configuration</span></span>

<span data-ttu-id="498d7-179">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="498d7-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="498d7-180">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="498d7-181">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="498d7-182">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="498d7-183">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="498d7-184">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="498d7-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="498d7-185">O provedor de `DOTNET_` variável de ambiente com argumentos de prefixo e linha de comando são incluídos por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="498d7-186">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="498d7-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="498d7-187">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="498d7-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="498d7-188">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="498d7-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="498d7-189">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="498d7-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="498d7-190">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="498d7-190">App configuration</span></span>

<span data-ttu-id="498d7-191">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="498d7-192">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="498d7-193">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="498d7-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="498d7-194">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="498d7-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="498d7-195">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="498d7-196">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="498d7-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="498d7-197">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="498d7-197">Settings for all app types</span></span>

<span data-ttu-id="498d7-198">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="498d7-199">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="498d7-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="498d7-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="498d7-200">ApplicationName</span></span>

<span data-ttu-id="498d7-201">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="498d7-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="498d7-202">**Chave:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="498d7-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="498d7-203">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-203">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-204">**Padrão**: O nome do conjunto que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="498d7-205">**Variável de ambiente:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="498d7-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="498d7-206">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="498d7-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="498d7-207">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-207">ContentRoot</span></span>

<span data-ttu-id="498d7-208">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="498d7-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="498d7-209">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="498d7-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="498d7-210">**Chave:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="498d7-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="498d7-211">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-211">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-212">**Padrão**: A pasta onde reside o conjunto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="498d7-213">**Variável de ambiente:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="498d7-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="498d7-214">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="498d7-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="498d7-215">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="498d7-215">For more information, see:</span></span>

* [<span data-ttu-id="498d7-216">Fundamentos: Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="498d7-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="498d7-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="498d7-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="498d7-218">EnvironmentName</span></span>

<span data-ttu-id="498d7-219">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="498d7-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="498d7-220">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="498d7-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="498d7-221">Valores não são sensíveis a casos.</span><span class="sxs-lookup"><span data-stu-id="498d7-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="498d7-222">**Chave:**`environment`</span><span class="sxs-lookup"><span data-stu-id="498d7-222">**Key**: `environment`</span></span>  
<span data-ttu-id="498d7-223">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-223">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-224">**Padrão:**`Production`</span><span class="sxs-lookup"><span data-stu-id="498d7-224">**Default**: `Production`</span></span>  
<span data-ttu-id="498d7-225">**Variável de ambiente:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="498d7-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="498d7-226">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="498d7-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="498d7-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="498d7-227">ShutdownTimeout</span></span>

<span data-ttu-id="498d7-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="498d7-229">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="498d7-229">The default value is five seconds.</span></span>  <span data-ttu-id="498d7-230">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="498d7-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="498d7-231">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="498d7-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="498d7-232">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="498d7-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="498d7-233">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="498d7-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="498d7-234">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="498d7-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="498d7-235">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="498d7-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="498d7-236">**Chave:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="498d7-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="498d7-237">**Tipo:**`int`</span><span class="sxs-lookup"><span data-stu-id="498d7-237">**Type**: `int`</span></span>  
<span data-ttu-id="498d7-238">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="498d7-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="498d7-239">**Variável de ambiente:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="498d7-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="498d7-240">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="498d7-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="498d7-241">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="498d7-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="498d7-242">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="498d7-242">Settings for web apps</span></span>

<span data-ttu-id="498d7-243">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="498d7-244">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="498d7-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="498d7-245">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="498d7-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="498d7-246">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="498d7-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="498d7-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="498d7-247">CaptureStartupErrors</span></span>

<span data-ttu-id="498d7-248">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="498d7-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="498d7-249">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="498d7-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="498d7-250">**Chave:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="498d7-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="498d7-251">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-252">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="498d7-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="498d7-253">**Variável de ambiente:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="498d7-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="498d7-254">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="498d7-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="498d7-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="498d7-255">DetailedErrors</span></span>

<span data-ttu-id="498d7-256">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="498d7-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="498d7-257">**Chave:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="498d7-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="498d7-258">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-259">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="498d7-259">**Default**: `false`</span></span>  
<span data-ttu-id="498d7-260">**Variável de ambiente:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="498d7-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="498d7-261">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="498d7-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="498d7-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="498d7-263">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="498d7-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="498d7-264">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="498d7-265">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="498d7-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="498d7-266">**Chave:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="498d7-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="498d7-267">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-267">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-268">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="498d7-268">**Default**: Empty string</span></span>  
<span data-ttu-id="498d7-269">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="498d7-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="498d7-270">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="498d7-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="498d7-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="498d7-272">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="498d7-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="498d7-273">**Chave:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="498d7-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="498d7-274">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-274">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-275">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="498d7-275">**Default**: Empty string</span></span>  
<span data-ttu-id="498d7-276">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="498d7-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="498d7-277">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="498d7-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="498d7-278">HTTPS_Port</span></span>

<span data-ttu-id="498d7-279">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="498d7-279">The HTTPS redirect port.</span></span> <span data-ttu-id="498d7-280">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="498d7-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="498d7-281">**Chave:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="498d7-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="498d7-282">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-282">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-283">**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="498d7-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="498d7-284">**Variável de ambiente:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="498d7-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="498d7-285">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="498d7-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="498d7-286">PreferHostingUrls</span></span>

<span data-ttu-id="498d7-287">Indica se o host deve ouvir as URLs configuradas com as `IWebHostBuilder` `IServer` URLs configuradas com a implementação.</span><span class="sxs-lookup"><span data-stu-id="498d7-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="498d7-288">**Chave:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="498d7-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="498d7-289">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-290">**Padrão:**`true`</span><span class="sxs-lookup"><span data-stu-id="498d7-290">**Default**: `true`</span></span>  
<span data-ttu-id="498d7-291">**Variável de ambiente:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="498d7-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="498d7-292">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="498d7-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="498d7-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="498d7-293">PreventHostingStartup</span></span>

<span data-ttu-id="498d7-294">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="498d7-295">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="498d7-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="498d7-296">**Chave:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="498d7-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="498d7-297">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-298">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="498d7-298">**Default**: `false`</span></span>  
<span data-ttu-id="498d7-299">**Variável de ambiente:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="498d7-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="498d7-300">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="498d7-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="498d7-301">StartupAssembly</span></span>

<span data-ttu-id="498d7-302">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="498d7-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="498d7-303">**Chave:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="498d7-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="498d7-304">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-304">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-305">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="498d7-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="498d7-306">**Variável de ambiente:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="498d7-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="498d7-307">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="498d7-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="498d7-308">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="498d7-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="498d7-309">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="498d7-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="498d7-310">URLs</span><span class="sxs-lookup"><span data-stu-id="498d7-310">URLs</span></span>

<span data-ttu-id="498d7-311">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="498d7-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="498d7-312">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="498d7-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="498d7-313">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="498d7-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="498d7-314">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="498d7-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="498d7-315">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="498d7-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="498d7-316">**Chave:**`urls`</span><span class="sxs-lookup"><span data-stu-id="498d7-316">**Key**: `urls`</span></span>  
<span data-ttu-id="498d7-317">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-317">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-318">**Padrão** `http://localhost:5000` : e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="498d7-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="498d7-319">**Variável de ambiente:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="498d7-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="498d7-320">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="498d7-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="498d7-321">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="498d7-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="498d7-322">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="498d7-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="498d7-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="498d7-323">WebRoot</span></span>

<span data-ttu-id="498d7-324">A propriedade [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo aos ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="498d7-325">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="498d7-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="498d7-326">**Chave:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="498d7-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="498d7-327">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-327">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-328">**Padrão**: O `wwwroot`padrão é .</span><span class="sxs-lookup"><span data-stu-id="498d7-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="498d7-329">O caminho para *{content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="498d7-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="498d7-330">**Variável de ambiente:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="498d7-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="498d7-331">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="498d7-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="498d7-332">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="498d7-332">For more information, see:</span></span>

* [<span data-ttu-id="498d7-333">Fundamentos: Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="498d7-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="498d7-334">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="498d7-335">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="498d7-335">Manage the host lifetime</span></span>

<span data-ttu-id="498d7-336">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="498d7-337">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="498d7-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="498d7-338">Executar</span><span class="sxs-lookup"><span data-stu-id="498d7-338">Run</span></span>

<span data-ttu-id="498d7-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="498d7-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="498d7-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-340">RunAsync</span></span>

<span data-ttu-id="498d7-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="498d7-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="498d7-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-342">RunConsoleAsync</span></span>

<span data-ttu-id="498d7-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permite o suporte ao console, constrói e inicia o host e aguarda o desligamento <kbd>do Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="498d7-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="498d7-344">Iniciar</span><span class="sxs-lookup"><span data-stu-id="498d7-344">Start</span></span>

<span data-ttu-id="498d7-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="498d7-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="498d7-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-346">StartAsync</span></span>

<span data-ttu-id="498d7-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="498d7-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="498d7-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="498d7-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="498d7-349">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="498d7-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="498d7-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-350">StopAsync</span></span>

<span data-ttu-id="498d7-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="498d7-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="498d7-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="498d7-352">WaitForShutdown</span></span>

<span data-ttu-id="498d7-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o segmento de chamada até que o desligamento seja acionado pelo IHostLifetime, como via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="498d7-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="498d7-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="498d7-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="498d7-356">Controle externo</span><span class="sxs-lookup"><span data-stu-id="498d7-356">External control</span></span>

<span data-ttu-id="498d7-357">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="498d7-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="498d7-358">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="498d7-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="498d7-359">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="498d7-360">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="498d7-361">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="498d7-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="498d7-362">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="498d7-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="498d7-363">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="498d7-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="498d7-364">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="498d7-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="498d7-365">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="498d7-366">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="498d7-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="498d7-367">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="498d7-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="498d7-368">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="498d7-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="498d7-369">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="498d7-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="498d7-370">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="498d7-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="498d7-371">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="498d7-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="498d7-372">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="498d7-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="498d7-373">Introdução</span><span class="sxs-lookup"><span data-stu-id="498d7-373">Introduction</span></span>

<span data-ttu-id="498d7-374">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="498d7-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="498d7-375">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="498d7-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="498d7-376"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="498d7-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="498d7-377">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="498d7-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="498d7-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="498d7-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="498d7-379">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="498d7-379">Set up a host</span></span>

<span data-ttu-id="498d7-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="498d7-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="498d7-381">Opções</span><span class="sxs-lookup"><span data-stu-id="498d7-381">Options</span></span>

<span data-ttu-id="498d7-382">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="498d7-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="498d7-383">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="498d7-383">Shutdown timeout</span></span>

<span data-ttu-id="498d7-384">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="498d7-385">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="498d7-385">The default value is five seconds.</span></span>

<span data-ttu-id="498d7-386">A seguinte configuração de opção aumenta `Program.Main` o tempo de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="498d7-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="498d7-387">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="498d7-387">Default services</span></span>

<span data-ttu-id="498d7-388">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="498d7-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="498d7-389">[Meio](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>Ambiente ( )</span><span class="sxs-lookup"><span data-stu-id="498d7-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="498d7-390">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="498d7-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="498d7-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="498d7-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="498d7-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="498d7-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="498d7-393">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="498d7-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="498d7-394">[Logging](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="498d7-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="498d7-395">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="498d7-395">Host configuration</span></span>

<span data-ttu-id="498d7-396">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="498d7-396">Host configuration is created by:</span></span>

* <span data-ttu-id="498d7-397">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="498d7-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="498d7-398">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="498d7-399">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="498d7-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="498d7-400">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="498d7-400">Application key (name)</span></span>

<span data-ttu-id="498d7-401">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="498d7-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="498d7-402">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="498d7-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="498d7-403">**Chave:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="498d7-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="498d7-404">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-404">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-405">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="498d7-406">**Conjunto usando:**`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="498d7-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="498d7-407">**Variável**de `<PREFIX_>APPLICATIONNAME` `<PREFIX_>` ambiente : (é [opcional e definido pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="498d7-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="498d7-408">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-408">Content root</span></span>

<span data-ttu-id="498d7-409">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="498d7-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="498d7-410">**Chave:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="498d7-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="498d7-411">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-411">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-412">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="498d7-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="498d7-413">**Conjunto usando:**`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="498d7-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="498d7-414">**Variável**de `<PREFIX_>CONTENTROOT` `<PREFIX_>` ambiente : (é [opcional e definido pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="498d7-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="498d7-415">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="498d7-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="498d7-416">Para obter mais informações, consulte [Fundamentos: Raiz de conteúdo](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="498d7-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="498d7-417">Ambiente</span><span class="sxs-lookup"><span data-stu-id="498d7-417">Environment</span></span>

<span data-ttu-id="498d7-418">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="498d7-419">**Chave:**`environment`</span><span class="sxs-lookup"><span data-stu-id="498d7-419">**Key**: `environment`</span></span>  
<span data-ttu-id="498d7-420">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-420">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-421">**Padrão:**`Production`</span><span class="sxs-lookup"><span data-stu-id="498d7-421">**Default**: `Production`</span></span>  
<span data-ttu-id="498d7-422">**Conjunto usando:**`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="498d7-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="498d7-423">**Variável**de `<PREFIX_>ENVIRONMENT` `<PREFIX_>` ambiente : (é [opcional e definido pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="498d7-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="498d7-424">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="498d7-424">The environment can be set to any value.</span></span> <span data-ttu-id="498d7-425">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="498d7-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="498d7-426">Valores não são sensíveis a casos.</span><span class="sxs-lookup"><span data-stu-id="498d7-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="498d7-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="498d7-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="498d7-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="498d7-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="498d7-429">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="498d7-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="498d7-431">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="498d7-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="498d7-432">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="498d7-432">No providers are included by default.</span></span> <span data-ttu-id="498d7-433">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="498d7-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="498d7-434">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="498d7-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="498d7-435">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="498d7-435">Environment variable configuration.</span></span>
* <span data-ttu-id="498d7-436">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="498d7-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="498d7-437">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="498d7-437">Any other required configuration providers.</span></span>

<span data-ttu-id="498d7-438">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="498d7-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="498d7-439">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="498d7-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="498d7-440">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="498d7-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="498d7-441">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="498d7-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="498d7-442">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="498d7-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="498d7-443">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="498d7-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="498d7-444">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="498d7-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="498d7-445">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="498d7-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="498d7-446">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="498d7-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="498d7-447">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="498d7-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="498d7-448">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="498d7-449">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="498d7-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="498d7-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="498d7-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="498d7-451">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="498d7-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="498d7-452">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="498d7-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="498d7-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="498d7-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="498d7-454">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="498d7-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="498d7-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="498d7-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="498d7-457">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="498d7-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="498d7-458">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="498d7-459">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="498d7-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="498d7-460">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="498d7-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="498d7-461">*appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="498d7-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="498d7-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="498d7-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="498d7-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="498d7-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="498d7-464">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="498d7-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="498d7-465">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="498d7-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="498d7-466">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="498d7-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="498d7-467">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="498d7-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="498d7-468">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="498d7-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="498d7-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="498d7-469">ConfigureServices</span></span>

<span data-ttu-id="498d7-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="498d7-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="498d7-472">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="498d7-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="498d7-473">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="498d7-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="498d7-474">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="498d7-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="498d7-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="498d7-475">ConfigureLogging</span></span>

<span data-ttu-id="498d7-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="498d7-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="498d7-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="498d7-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-478">UseConsoleLifetime</span></span>

<span data-ttu-id="498d7-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>ouve <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chamadas <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="498d7-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="498d7-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="498d7-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="498d7-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="498d7-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="498d7-482">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="498d7-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="498d7-483">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="498d7-483">Container configuration</span></span>

<span data-ttu-id="498d7-484">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="498d7-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="498d7-485">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="498d7-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="498d7-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="498d7-487">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="498d7-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="498d7-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="498d7-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="498d7-490">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="498d7-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="498d7-491">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="498d7-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="498d7-492">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="498d7-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="498d7-493">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="498d7-493">Extensibility</span></span>

<span data-ttu-id="498d7-494">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="498d7-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="498d7-495">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="498d7-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="498d7-496">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="498d7-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="498d7-497">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="498d7-497">Manage the host</span></span>

<span data-ttu-id="498d7-498">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="498d7-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="498d7-499">Executar</span><span class="sxs-lookup"><span data-stu-id="498d7-499">Run</span></span>

<span data-ttu-id="498d7-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="498d7-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="498d7-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-501">RunAsync</span></span>

<span data-ttu-id="498d7-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="498d7-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="498d7-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-503">RunConsoleAsync</span></span>

<span data-ttu-id="498d7-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permite o suporte ao console, constrói e inicia o host e aguarda o desligamento <kbd>do Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="498d7-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="498d7-505">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-505">Start and StopAsync</span></span>

<span data-ttu-id="498d7-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="498d7-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="498d7-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="498d7-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="498d7-508">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="498d7-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="498d7-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="498d7-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="498d7-511">WaitForShutdown</span></span>

<span data-ttu-id="498d7-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é acionado através <xref:Microsoft.Extensions.Hosting.IHostLifetime>do `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , tais como (escuta <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="498d7-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="498d7-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="498d7-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="498d7-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="498d7-516">Controle externo</span><span class="sxs-lookup"><span data-stu-id="498d7-516">External control</span></span>

<span data-ttu-id="498d7-517">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="498d7-517">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="498d7-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="498d7-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="498d7-519">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="498d7-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="498d7-520">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="498d7-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="498d7-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="498d7-522">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="498d7-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="498d7-523">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="498d7-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="498d7-524">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="498d7-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="498d7-526">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="498d7-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="498d7-527">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="498d7-527">Cancellation Token</span></span> | <span data-ttu-id="498d7-528">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="498d7-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="498d7-529">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="498d7-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="498d7-530">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="498d7-531">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="498d7-531">All requests should be processed.</span></span> <span data-ttu-id="498d7-532">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="498d7-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="498d7-533">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="498d7-534">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="498d7-534">Requests may still be processing.</span></span> <span data-ttu-id="498d7-535">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="498d7-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="498d7-536">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="498d7-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="498d7-537">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="498d7-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="498d7-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="498d7-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="498d7-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="498d7-540">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="498d7-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="498d7-541">Os modelos ASP.NET Core criam um<xref:Microsoft.Extensions.Hosting.HostBuilder>Host Genérico .NET Core ().</span><span class="sxs-lookup"><span data-stu-id="498d7-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="498d7-542">Definição do host</span><span class="sxs-lookup"><span data-stu-id="498d7-542">Host definition</span></span>

<span data-ttu-id="498d7-543">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="498d7-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="498d7-544">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="498d7-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="498d7-545">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="498d7-545">Logging</span></span>
* <span data-ttu-id="498d7-546">Configuração</span><span class="sxs-lookup"><span data-stu-id="498d7-546">Configuration</span></span>
* <span data-ttu-id="498d7-547">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="498d7-547">`IHostedService` implementations</span></span>

<span data-ttu-id="498d7-548">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="498d7-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="498d7-549">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="498d7-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="498d7-550">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="498d7-551">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="498d7-551">Set up a host</span></span>

<span data-ttu-id="498d7-552">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="498d7-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="498d7-553">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="498d7-553">The `Main` method:</span></span>

* <span data-ttu-id="498d7-554">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="498d7-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="498d7-555">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="498d7-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="498d7-556">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="498d7-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="498d7-557">O código a seguir cria uma `IHostedService` carga de trabalho não-HTTP com uma implementação adicionada ao contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="498d7-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="498d7-558">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="498d7-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="498d7-559">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="498d7-560">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="498d7-561">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="498d7-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="498d7-562">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="498d7-562">Default builder settings</span></span>

<span data-ttu-id="498d7-563">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="498d7-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="498d7-564">Define a raiz de [conteúdo](xref:fundamentals/index#content-root) <xref:System.IO.Directory.GetCurrentDirectory*>para o caminho retornado por .</span><span class="sxs-lookup"><span data-stu-id="498d7-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="498d7-565">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="498d7-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="498d7-566">Variáveis de ambiente `DOTNET_`prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="498d7-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="498d7-567">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="498d7-567">Command-line arguments.</span></span>
* <span data-ttu-id="498d7-568">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="498d7-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="498d7-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="498d7-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="498d7-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="498d7-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="498d7-571">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="498d7-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="498d7-572">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="498d7-572">Environment variables.</span></span>
  * <span data-ttu-id="498d7-573">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="498d7-573">Command-line arguments.</span></span>
* <span data-ttu-id="498d7-574">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="498d7-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="498d7-575">Console</span><span class="sxs-lookup"><span data-stu-id="498d7-575">Console</span></span>
  * <span data-ttu-id="498d7-576">Depurar</span><span class="sxs-lookup"><span data-stu-id="498d7-576">Debug</span></span>
  * <span data-ttu-id="498d7-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="498d7-577">EventSource</span></span>
  * <span data-ttu-id="498d7-578">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="498d7-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="498d7-579">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="498d7-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="498d7-580">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="498d7-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="498d7-581">Carrega a configuração do host `ASPNETCORE_`a partir de variáveis de ambiente prefixadas com .</span><span class="sxs-lookup"><span data-stu-id="498d7-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="498d7-582">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="498d7-583">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="498d7-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="498d7-584">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="498d7-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="498d7-585">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true`se for igual .</span><span class="sxs-lookup"><span data-stu-id="498d7-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="498d7-586">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="498d7-586">Enables IIS integration.</span></span> <span data-ttu-id="498d7-587">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="498d7-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="498d7-588">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="498d7-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="498d7-589">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="498d7-589">Framework-provided services</span></span>

<span data-ttu-id="498d7-590">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="498d7-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="498d7-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="498d7-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="498d7-593">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="498d7-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="498d7-594">Para obter mais informações sobre serviços fornecidos por estruturas, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="498d7-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="498d7-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="498d7-596">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="498d7-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="498d7-597">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="498d7-598">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="498d7-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="498d7-599">O exemplo a `IHostedService` seguir é `IHostApplicationLifetime` uma implementação que registra eventos:</span><span class="sxs-lookup"><span data-stu-id="498d7-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="498d7-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="498d7-600">IHostLifetime</span></span>

<span data-ttu-id="498d7-601">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="498d7-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="498d7-602">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="498d7-602">The last implementation registered is used.</span></span>

<span data-ttu-id="498d7-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="498d7-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="498d7-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="498d7-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="498d7-605">Ouve <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="498d7-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="498d7-606">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="498d7-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="498d7-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="498d7-607">IHostEnvironment</span></span>

<span data-ttu-id="498d7-608">Injete o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="498d7-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="498d7-609">Applicationname</span><span class="sxs-lookup"><span data-stu-id="498d7-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="498d7-610">Nome do ambiente</span><span class="sxs-lookup"><span data-stu-id="498d7-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="498d7-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="498d7-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="498d7-612">Os aplicativos `IWebHostEnvironment` da Web implementam a interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="498d7-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="498d7-613">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="498d7-613">Host configuration</span></span>

<span data-ttu-id="498d7-614">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="498d7-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="498d7-615">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="498d7-616">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="498d7-617">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="498d7-618">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="498d7-619">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="498d7-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="498d7-620">O provedor de `DOTNET_` variável de ambiente com argumentos de prefixo e linha de comando são incluídos por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="498d7-621">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="498d7-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="498d7-622">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="498d7-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="498d7-623">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="498d7-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="498d7-624">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="498d7-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="498d7-625">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="498d7-625">App configuration</span></span>

<span data-ttu-id="498d7-626">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="498d7-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="498d7-627">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="498d7-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="498d7-628">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="498d7-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="498d7-629">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="498d7-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="498d7-630">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="498d7-631">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="498d7-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="498d7-632">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="498d7-632">Settings for all app types</span></span>

<span data-ttu-id="498d7-633">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="498d7-634">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="498d7-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="498d7-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="498d7-635">ApplicationName</span></span>

<span data-ttu-id="498d7-636">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="498d7-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="498d7-637">**Chave:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="498d7-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="498d7-638">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-638">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-639">**Padrão**: O nome do conjunto que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="498d7-640">**Variável de ambiente:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="498d7-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="498d7-641">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="498d7-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="498d7-642">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-642">ContentRoot</span></span>

<span data-ttu-id="498d7-643">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="498d7-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="498d7-644">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="498d7-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="498d7-645">**Chave:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="498d7-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="498d7-646">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-646">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-647">**Padrão**: A pasta onde reside o conjunto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="498d7-648">**Variável de ambiente:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="498d7-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="498d7-649">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="498d7-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="498d7-650">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="498d7-650">For more information, see:</span></span>

* [<span data-ttu-id="498d7-651">Fundamentos: Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="498d7-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="498d7-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="498d7-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="498d7-653">EnvironmentName</span></span>

<span data-ttu-id="498d7-654">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="498d7-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="498d7-655">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="498d7-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="498d7-656">Valores não são sensíveis a casos.</span><span class="sxs-lookup"><span data-stu-id="498d7-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="498d7-657">**Chave:**`environment`</span><span class="sxs-lookup"><span data-stu-id="498d7-657">**Key**: `environment`</span></span>  
<span data-ttu-id="498d7-658">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-658">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-659">**Padrão:**`Production`</span><span class="sxs-lookup"><span data-stu-id="498d7-659">**Default**: `Production`</span></span>  
<span data-ttu-id="498d7-660">**Variável de ambiente:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="498d7-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="498d7-661">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="498d7-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="498d7-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="498d7-662">ShutdownTimeout</span></span>

<span data-ttu-id="498d7-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="498d7-664">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="498d7-664">The default value is five seconds.</span></span>  <span data-ttu-id="498d7-665">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="498d7-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="498d7-666">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="498d7-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="498d7-667">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="498d7-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="498d7-668">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="498d7-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="498d7-669">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="498d7-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="498d7-670">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="498d7-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="498d7-671">**Chave:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="498d7-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="498d7-672">**Tipo:**`int`</span><span class="sxs-lookup"><span data-stu-id="498d7-672">**Type**: `int`</span></span>  
<span data-ttu-id="498d7-673">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="498d7-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="498d7-674">**Variável de ambiente:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="498d7-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="498d7-675">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="498d7-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="498d7-676">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="498d7-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="498d7-677">Desativar a recarga da configuração do aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="498d7-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="498d7-678">Por [padrão,](xref:fundamentals/configuration/index#default) *appsettings.json* e *appsettings.{ O ambiente}.json* é recarregado quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="498d7-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="498d7-679">Para desativar esse comportamento de recarga no ASP.NET O Núcleo `hostBuilder:reloadConfigOnChange` 5.0 Visualização 3 ou posterior, defina a chave para `false`.</span><span class="sxs-lookup"><span data-stu-id="498d7-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="498d7-680">**Chave:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="498d7-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="498d7-681">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-682">**Padrão:**`true`</span><span class="sxs-lookup"><span data-stu-id="498d7-682">**Default**: `true`</span></span>  
<span data-ttu-id="498d7-683">**Argumento de linha de comando:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="498d7-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="498d7-684">**Variável de ambiente:**`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="498d7-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="498d7-685">O separador de cólon`:`não funciona com teclas hierárquicas variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="498d7-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="498d7-686">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="498d7-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="498d7-687">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="498d7-687">Settings for web apps</span></span>

<span data-ttu-id="498d7-688">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="498d7-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="498d7-689">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="498d7-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="498d7-690">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="498d7-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="498d7-691">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="498d7-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="498d7-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="498d7-692">CaptureStartupErrors</span></span>

<span data-ttu-id="498d7-693">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="498d7-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="498d7-694">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="498d7-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="498d7-695">**Chave:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="498d7-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="498d7-696">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-697">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="498d7-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="498d7-698">**Variável de ambiente:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="498d7-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="498d7-699">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="498d7-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="498d7-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="498d7-700">DetailedErrors</span></span>

<span data-ttu-id="498d7-701">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="498d7-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="498d7-702">**Chave:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="498d7-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="498d7-703">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-704">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="498d7-704">**Default**: `false`</span></span>  
<span data-ttu-id="498d7-705">**Variável de ambiente:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="498d7-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="498d7-706">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="498d7-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="498d7-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="498d7-708">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="498d7-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="498d7-709">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="498d7-710">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="498d7-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="498d7-711">**Chave:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="498d7-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="498d7-712">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-712">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-713">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="498d7-713">**Default**: Empty string</span></span>  
<span data-ttu-id="498d7-714">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="498d7-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="498d7-715">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="498d7-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="498d7-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="498d7-717">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="498d7-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="498d7-718">**Chave:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="498d7-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="498d7-719">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-719">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-720">**Padrão**: Cadeia de seqüência vazia</span><span class="sxs-lookup"><span data-stu-id="498d7-720">**Default**: Empty string</span></span>  
<span data-ttu-id="498d7-721">**Variável de ambiente:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="498d7-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="498d7-722">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="498d7-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="498d7-723">HTTPS_Port</span></span>

<span data-ttu-id="498d7-724">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="498d7-724">The HTTPS redirect port.</span></span> <span data-ttu-id="498d7-725">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="498d7-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="498d7-726">**Chave:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="498d7-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="498d7-727">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-727">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-728">**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="498d7-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="498d7-729">**Variável de ambiente:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="498d7-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="498d7-730">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="498d7-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="498d7-731">PreferHostingUrls</span></span>

<span data-ttu-id="498d7-732">Indica se o host deve ouvir as URLs configuradas com as `IWebHostBuilder` `IServer` URLs configuradas com a implementação.</span><span class="sxs-lookup"><span data-stu-id="498d7-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="498d7-733">**Chave:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="498d7-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="498d7-734">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-735">**Padrão:**`true`</span><span class="sxs-lookup"><span data-stu-id="498d7-735">**Default**: `true`</span></span>  
<span data-ttu-id="498d7-736">**Variável de ambiente:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="498d7-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="498d7-737">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="498d7-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="498d7-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="498d7-738">PreventHostingStartup</span></span>

<span data-ttu-id="498d7-739">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="498d7-740">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="498d7-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="498d7-741">**Chave:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="498d7-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="498d7-742">**Tipo** `bool` :`true` `1`(ou )</span><span class="sxs-lookup"><span data-stu-id="498d7-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="498d7-743">**Padrão:**`false`</span><span class="sxs-lookup"><span data-stu-id="498d7-743">**Default**: `false`</span></span>  
<span data-ttu-id="498d7-744">**Variável de ambiente:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="498d7-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="498d7-745">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="498d7-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="498d7-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="498d7-746">StartupAssembly</span></span>

<span data-ttu-id="498d7-747">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="498d7-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="498d7-748">**Chave:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="498d7-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="498d7-749">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-749">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-750">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="498d7-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="498d7-751">**Variável de ambiente:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="498d7-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="498d7-752">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="498d7-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="498d7-753">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="498d7-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="498d7-754">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="498d7-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="498d7-755">URLs</span><span class="sxs-lookup"><span data-stu-id="498d7-755">URLs</span></span>

<span data-ttu-id="498d7-756">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="498d7-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="498d7-757">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="498d7-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="498d7-758">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="498d7-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="498d7-759">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="498d7-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="498d7-760">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="498d7-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="498d7-761">**Chave:**`urls`</span><span class="sxs-lookup"><span data-stu-id="498d7-761">**Key**: `urls`</span></span>  
<span data-ttu-id="498d7-762">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-762">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-763">**Padrão** `http://localhost:5000` : e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="498d7-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="498d7-764">**Variável de ambiente:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="498d7-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="498d7-765">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="498d7-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="498d7-766">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="498d7-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="498d7-767">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="498d7-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="498d7-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="498d7-768">WebRoot</span></span>

<span data-ttu-id="498d7-769">A propriedade [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo aos ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="498d7-770">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="498d7-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="498d7-771">**Chave:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="498d7-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="498d7-772">**Tipo:**`string`</span><span class="sxs-lookup"><span data-stu-id="498d7-772">**Type**: `string`</span></span>  
<span data-ttu-id="498d7-773">**Padrão**: O `wwwroot`padrão é .</span><span class="sxs-lookup"><span data-stu-id="498d7-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="498d7-774">O caminho para *{content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="498d7-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="498d7-775">**Variável de ambiente:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="498d7-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="498d7-776">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="498d7-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="498d7-777">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="498d7-777">For more information, see:</span></span>

* [<span data-ttu-id="498d7-778">Fundamentos: Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="498d7-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="498d7-779">Raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="498d7-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="498d7-780">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="498d7-780">Manage the host lifetime</span></span>

<span data-ttu-id="498d7-781">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="498d7-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="498d7-782">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="498d7-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="498d7-783">Executar</span><span class="sxs-lookup"><span data-stu-id="498d7-783">Run</span></span>

<span data-ttu-id="498d7-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="498d7-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="498d7-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-785">RunAsync</span></span>

<span data-ttu-id="498d7-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="498d7-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="498d7-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-787">RunConsoleAsync</span></span>

<span data-ttu-id="498d7-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permite o suporte ao console, constrói e inicia o host e aguarda o desligamento <kbd>do Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="498d7-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="498d7-789">Iniciar</span><span class="sxs-lookup"><span data-stu-id="498d7-789">Start</span></span>

<span data-ttu-id="498d7-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="498d7-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="498d7-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-791">StartAsync</span></span>

<span data-ttu-id="498d7-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="498d7-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="498d7-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="498d7-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="498d7-794">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="498d7-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="498d7-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-795">StopAsync</span></span>

<span data-ttu-id="498d7-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="498d7-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="498d7-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="498d7-797">WaitForShutdown</span></span>

<span data-ttu-id="498d7-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o segmento de chamada até que o desligamento seja acionado pelo IHostLifetime, como via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="498d7-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="498d7-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="498d7-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="498d7-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="498d7-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="498d7-801">Controle externo</span><span class="sxs-lookup"><span data-stu-id="498d7-801">External control</span></span>

<span data-ttu-id="498d7-802">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="498d7-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="498d7-803">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="498d7-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
