---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 26aef561ba299403df0dad9893fecd5e2a15ab0e
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213005"
---
# <a name="net-generic-host"></a><span data-ttu-id="32b37-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="32b37-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="32b37-104">Os modelos de ASP.NET Core criam um host genérico do .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="32b37-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="32b37-105">Definição de host</span><span class="sxs-lookup"><span data-stu-id="32b37-105">Host definition</span></span>

<span data-ttu-id="32b37-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="32b37-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="32b37-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="32b37-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="32b37-108">Registro em log</span><span class="sxs-lookup"><span data-stu-id="32b37-108">Logging</span></span>
* <span data-ttu-id="32b37-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="32b37-109">Configuration</span></span>
* <span data-ttu-id="32b37-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="32b37-110">`IHostedService` implementations</span></span>

<span data-ttu-id="32b37-111">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="32b37-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="32b37-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="32b37-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="32b37-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="32b37-114">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="32b37-114">Set up a host</span></span>

<span data-ttu-id="32b37-115">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="32b37-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="32b37-116">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="32b37-116">The `Main` method:</span></span>

* <span data-ttu-id="32b37-117">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="32b37-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="32b37-118">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="32b37-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="32b37-119">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host genérico:</span><span class="sxs-lookup"><span data-stu-id="32b37-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="32b37-120">O código a seguir cria um host genérico usando carga de trabalho não HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="32b37-121">A `IHostedService` implementação é adicionada ao contêiner di:</span><span class="sxs-lookup"><span data-stu-id="32b37-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="32b37-122">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="32b37-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="32b37-123">O código anterior é gerado pelos modelos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32b37-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="32b37-124">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="32b37-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="32b37-125">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="32b37-126">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="32b37-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="32b37-127">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="32b37-127">Default builder settings</span></span>

<span data-ttu-id="32b37-128">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="32b37-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="32b37-129">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="32b37-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="32b37-130">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="32b37-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="32b37-131">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="32b37-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="32b37-132">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="32b37-132">Command-line arguments.</span></span>
* <span data-ttu-id="32b37-133">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="32b37-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="32b37-134">*appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="32b37-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="32b37-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="32b37-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="32b37-136">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="32b37-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="32b37-137">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="32b37-137">Environment variables.</span></span>
  * <span data-ttu-id="32b37-138">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="32b37-138">Command-line arguments.</span></span>
* <span data-ttu-id="32b37-139">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="32b37-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="32b37-140">Console</span><span class="sxs-lookup"><span data-stu-id="32b37-140">Console</span></span>
  * <span data-ttu-id="32b37-141">Depurar</span><span class="sxs-lookup"><span data-stu-id="32b37-141">Debug</span></span>
  * <span data-ttu-id="32b37-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="32b37-142">EventSource</span></span>
  * <span data-ttu-id="32b37-143">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="32b37-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="32b37-144">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="32b37-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="32b37-145">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="32b37-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="32b37-146">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="32b37-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="32b37-147">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="32b37-148">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="32b37-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="32b37-149">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="32b37-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="32b37-150">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="32b37-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="32b37-151">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="32b37-151">Enables IIS integration.</span></span> <span data-ttu-id="32b37-152">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="32b37-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="32b37-153">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="32b37-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="32b37-154">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="32b37-154">Framework-provided services</span></span>

<span data-ttu-id="32b37-155">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="32b37-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="32b37-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="32b37-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="32b37-158">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="32b37-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="32b37-159">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="32b37-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="32b37-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="32b37-161">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="32b37-162">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="32b37-163">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="32b37-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="32b37-164">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="32b37-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="32b37-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-165">IHostLifetime</span></span>

<span data-ttu-id="32b37-166">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="32b37-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="32b37-167">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="32b37-167">The last implementation registered is used.</span></span>

<span data-ttu-id="32b37-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="32b37-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="32b37-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="32b37-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="32b37-170">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="32b37-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="32b37-171">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="32b37-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="32b37-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="32b37-172">IHostEnvironment</span></span>

<span data-ttu-id="32b37-173">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="32b37-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="32b37-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="32b37-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="32b37-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="32b37-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="32b37-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="32b37-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="32b37-177">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="32b37-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="32b37-178">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="32b37-178">Host configuration</span></span>

<span data-ttu-id="32b37-179">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="32b37-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="32b37-180">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="32b37-181">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="32b37-182">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="32b37-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="32b37-183">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="32b37-184">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="32b37-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="32b37-185">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="32b37-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="32b37-186">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="32b37-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="32b37-187">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="32b37-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="32b37-188">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="32b37-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="32b37-189">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="32b37-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="32b37-190">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="32b37-190">App configuration</span></span>

<span data-ttu-id="32b37-191">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="32b37-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="32b37-192">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="32b37-193">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="32b37-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="32b37-194">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="32b37-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="32b37-195">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="32b37-196">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="32b37-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="32b37-197">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="32b37-197">Settings for all app types</span></span>

<span data-ttu-id="32b37-198">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="32b37-199">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="32b37-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="32b37-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="32b37-200">ApplicationName</span></span>

<span data-ttu-id="32b37-201">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="32b37-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="32b37-202">**Chave**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="32b37-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="32b37-203">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-203">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-204">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="32b37-205">**Variável de ambiente**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="32b37-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="32b37-206">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="32b37-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="32b37-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-207">ContentRoot</span></span>

<span data-ttu-id="32b37-208">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="32b37-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="32b37-209">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="32b37-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="32b37-210">**Chave**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="32b37-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="32b37-211">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-211">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-212">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="32b37-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="32b37-213">**Variável de ambiente**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="32b37-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="32b37-214">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="32b37-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="32b37-215">Para obter mais informações, confira:</span><span class="sxs-lookup"><span data-stu-id="32b37-215">For more information, see:</span></span>

* [<span data-ttu-id="32b37-216">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="32b37-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="32b37-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="32b37-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="32b37-218">EnvironmentName</span></span>

<span data-ttu-id="32b37-219">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="32b37-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="32b37-220">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="32b37-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="32b37-221">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="32b37-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="32b37-222">**Chave**:`environment`</span><span class="sxs-lookup"><span data-stu-id="32b37-222">**Key**: `environment`</span></span>  
<span data-ttu-id="32b37-223">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-223">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-224">**Padrão**:`Production`</span><span class="sxs-lookup"><span data-stu-id="32b37-224">**Default**: `Production`</span></span>  
<span data-ttu-id="32b37-225">**Variável de ambiente**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="32b37-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="32b37-226">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="32b37-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="32b37-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="32b37-227">ShutdownTimeout</span></span>

<span data-ttu-id="32b37-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="32b37-229">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="32b37-229">The default value is five seconds.</span></span>  <span data-ttu-id="32b37-230">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="32b37-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="32b37-231">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="32b37-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="32b37-232">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="32b37-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="32b37-233">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="32b37-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="32b37-234">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="32b37-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="32b37-235">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="32b37-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="32b37-236">**Chave**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="32b37-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="32b37-237">**Tipo**:`int`</span><span class="sxs-lookup"><span data-stu-id="32b37-237">**Type**: `int`</span></span>  
<span data-ttu-id="32b37-238">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="32b37-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="32b37-239">**Variável de ambiente**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="32b37-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="32b37-240">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="32b37-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="32b37-241">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="32b37-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="32b37-242">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="32b37-242">Settings for web apps</span></span>

<span data-ttu-id="32b37-243">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="32b37-244">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="32b37-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="32b37-245">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="32b37-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="32b37-246">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="32b37-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="32b37-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="32b37-247">CaptureStartupErrors</span></span>

<span data-ttu-id="32b37-248">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="32b37-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="32b37-249">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="32b37-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="32b37-250">**Chave**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="32b37-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="32b37-251">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-252">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="32b37-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="32b37-253">**Variável de ambiente**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="32b37-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="32b37-254">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="32b37-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="32b37-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="32b37-255">DetailedErrors</span></span>

<span data-ttu-id="32b37-256">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="32b37-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="32b37-257">**Chave**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="32b37-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="32b37-258">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-259">**Padrão**:`false`</span><span class="sxs-lookup"><span data-stu-id="32b37-259">**Default**: `false`</span></span>  
<span data-ttu-id="32b37-260">**Variável de ambiente**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="32b37-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="32b37-261">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="32b37-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="32b37-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="32b37-263">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="32b37-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="32b37-264">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="32b37-265">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="32b37-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="32b37-266">**Chave**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="32b37-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="32b37-267">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-267">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-268">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="32b37-268">**Default**: Empty string</span></span>  
<span data-ttu-id="32b37-269">**Variável de ambiente**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="32b37-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="32b37-270">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="32b37-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="32b37-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="32b37-272">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="32b37-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="32b37-273">**Chave**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="32b37-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="32b37-274">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-274">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-275">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="32b37-275">**Default**: Empty string</span></span>  
<span data-ttu-id="32b37-276">**Variável de ambiente**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="32b37-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="32b37-277">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="32b37-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="32b37-278">HTTPS_Port</span></span>

<span data-ttu-id="32b37-279">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="32b37-279">The HTTPS redirect port.</span></span> <span data-ttu-id="32b37-280">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="32b37-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="32b37-281">**Chave**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="32b37-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="32b37-282">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-282">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-283">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="32b37-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="32b37-284">**Variável de ambiente**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="32b37-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="32b37-285">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="32b37-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="32b37-286">PreferHostingUrls</span></span>

<span data-ttu-id="32b37-287">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="32b37-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="32b37-288">**Chave**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="32b37-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="32b37-289">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-290">**Padrão**:`true`</span><span class="sxs-lookup"><span data-stu-id="32b37-290">**Default**: `true`</span></span>  
<span data-ttu-id="32b37-291">**Variável de ambiente**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="32b37-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="32b37-292">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="32b37-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="32b37-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="32b37-293">PreventHostingStartup</span></span>

<span data-ttu-id="32b37-294">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="32b37-295">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="32b37-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="32b37-296">**Chave**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="32b37-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="32b37-297">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-298">**Padrão**:`false`</span><span class="sxs-lookup"><span data-stu-id="32b37-298">**Default**: `false`</span></span>  
<span data-ttu-id="32b37-299">**Variável de ambiente**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="32b37-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="32b37-300">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="32b37-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="32b37-301">StartupAssembly</span></span>

<span data-ttu-id="32b37-302">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="32b37-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="32b37-303">**Chave**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="32b37-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="32b37-304">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-304">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-305">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="32b37-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="32b37-306">**Variável de ambiente**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="32b37-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="32b37-307">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="32b37-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="32b37-308">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="32b37-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="32b37-309">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="32b37-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="32b37-310">URLs</span><span class="sxs-lookup"><span data-stu-id="32b37-310">URLs</span></span>

<span data-ttu-id="32b37-311">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="32b37-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="32b37-312">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="32b37-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="32b37-313">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="32b37-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="32b37-314">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="32b37-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="32b37-315">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="32b37-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="32b37-316">**Chave**:`urls`</span><span class="sxs-lookup"><span data-stu-id="32b37-316">**Key**: `urls`</span></span>  
<span data-ttu-id="32b37-317">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-317">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-318">**Padrão**: `http://localhost:5000` e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="32b37-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="32b37-319">**Variável de ambiente**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="32b37-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="32b37-320">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="32b37-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="32b37-321">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="32b37-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="32b37-322">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="32b37-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="32b37-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-323">WebRoot</span></span>

<span data-ttu-id="32b37-324">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="32b37-325">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="32b37-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="32b37-326">**Chave**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="32b37-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="32b37-327">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-327">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-328">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="32b37-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="32b37-329">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="32b37-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="32b37-330">**Variável de ambiente**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="32b37-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="32b37-331">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="32b37-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="32b37-332">Para obter mais informações, confira:</span><span class="sxs-lookup"><span data-stu-id="32b37-332">For more information, see:</span></span>

* [<span data-ttu-id="32b37-333">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="32b37-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="32b37-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="32b37-335">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="32b37-335">Manage the host lifetime</span></span>

<span data-ttu-id="32b37-336">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="32b37-337">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="32b37-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="32b37-338">Executar</span><span class="sxs-lookup"><span data-stu-id="32b37-338">Run</span></span>

<span data-ttu-id="32b37-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="32b37-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="32b37-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-340">RunAsync</span></span>

<span data-ttu-id="32b37-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="32b37-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="32b37-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-342">RunConsoleAsync</span></span>

<span data-ttu-id="32b37-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="32b37-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="32b37-344">Iniciar</span><span class="sxs-lookup"><span data-stu-id="32b37-344">Start</span></span>

<span data-ttu-id="32b37-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="32b37-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="32b37-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-346">StartAsync</span></span>

<span data-ttu-id="32b37-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="32b37-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="32b37-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="32b37-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="32b37-349">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="32b37-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="32b37-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-350">StopAsync</span></span>

<span data-ttu-id="32b37-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="32b37-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="32b37-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="32b37-352">WaitForShutdown</span></span>

<span data-ttu-id="32b37-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="32b37-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="32b37-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="32b37-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="32b37-356">Controle externo</span><span class="sxs-lookup"><span data-stu-id="32b37-356">External control</span></span>

<span data-ttu-id="32b37-357">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="32b37-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="32b37-358">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="32b37-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="32b37-359">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="32b37-360">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="32b37-361">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="32b37-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="32b37-362">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="32b37-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="32b37-363">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="32b37-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="32b37-364">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="32b37-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="32b37-365">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="32b37-366">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="32b37-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="32b37-367">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="32b37-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="32b37-368">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="32b37-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="32b37-369">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="32b37-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="32b37-370">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="32b37-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="32b37-371">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="32b37-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="32b37-372">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="32b37-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="32b37-373">Introdução</span><span class="sxs-lookup"><span data-stu-id="32b37-373">Introduction</span></span>

<span data-ttu-id="32b37-374">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="32b37-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="32b37-375">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="32b37-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="32b37-376"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="32b37-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="32b37-377">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="32b37-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="32b37-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="32b37-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="32b37-379">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="32b37-379">Set up a host</span></span>

<span data-ttu-id="32b37-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="32b37-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="32b37-381">Opções</span><span class="sxs-lookup"><span data-stu-id="32b37-381">Options</span></span>

<span data-ttu-id="32b37-382">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="32b37-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="32b37-383">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="32b37-383">Shutdown timeout</span></span>

<span data-ttu-id="32b37-384">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="32b37-385">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="32b37-385">The default value is five seconds.</span></span>

<span data-ttu-id="32b37-386">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundo:</span><span class="sxs-lookup"><span data-stu-id="32b37-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="32b37-387">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="32b37-387">Default services</span></span>

<span data-ttu-id="32b37-388">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="32b37-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="32b37-389">[Ambiente](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="32b37-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="32b37-390">[Configuração](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="32b37-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="32b37-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="32b37-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="32b37-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="32b37-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="32b37-393">[Opções](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="32b37-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="32b37-394">[Registro em log](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="32b37-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="32b37-395">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="32b37-395">Host configuration</span></span>

<span data-ttu-id="32b37-396">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="32b37-396">Host configuration is created by:</span></span>

* <span data-ttu-id="32b37-397">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="32b37-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="32b37-398">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="32b37-399">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="32b37-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="32b37-400">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="32b37-400">Application key (name)</span></span>

<span data-ttu-id="32b37-401">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="32b37-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="32b37-402">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="32b37-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="32b37-403">**Chave**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="32b37-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="32b37-404">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-404">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-405">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="32b37-406">**Definir usando**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="32b37-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="32b37-407">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="32b37-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="32b37-408">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="32b37-408">Content root</span></span>

<span data-ttu-id="32b37-409">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="32b37-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="32b37-410">**Chave**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="32b37-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="32b37-411">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-411">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-412">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="32b37-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="32b37-413">**Definir usando**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="32b37-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="32b37-414">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="32b37-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="32b37-415">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="32b37-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="32b37-416">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="32b37-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="32b37-417">Ambiente</span><span class="sxs-lookup"><span data-stu-id="32b37-417">Environment</span></span>

<span data-ttu-id="32b37-418">Define o [ambiente](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="32b37-419">**Chave**:`environment`</span><span class="sxs-lookup"><span data-stu-id="32b37-419">**Key**: `environment`</span></span>  
<span data-ttu-id="32b37-420">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-420">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-421">**Padrão**:`Production`</span><span class="sxs-lookup"><span data-stu-id="32b37-421">**Default**: `Production`</span></span>  
<span data-ttu-id="32b37-422">**Definir usando**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="32b37-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="32b37-423">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="32b37-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="32b37-424">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="32b37-424">The environment can be set to any value.</span></span> <span data-ttu-id="32b37-425">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="32b37-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="32b37-426">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="32b37-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="32b37-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="32b37-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="32b37-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="32b37-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="32b37-429">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="32b37-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="32b37-431">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="32b37-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="32b37-432">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="32b37-432">No providers are included by default.</span></span> <span data-ttu-id="32b37-433">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="32b37-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="32b37-434">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="32b37-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="32b37-435">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="32b37-435">Environment variable configuration.</span></span>
* <span data-ttu-id="32b37-436">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="32b37-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="32b37-437">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="32b37-437">Any other required configuration providers.</span></span>

<span data-ttu-id="32b37-438">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="32b37-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="32b37-439">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="32b37-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="32b37-440">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="32b37-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="32b37-441">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="32b37-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="32b37-442">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="32b37-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="32b37-443">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="32b37-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="32b37-444">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="32b37-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="32b37-445">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="32b37-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="32b37-446">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="32b37-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="32b37-447">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="32b37-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="32b37-448">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="32b37-449">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="32b37-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="32b37-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="32b37-450">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="32b37-451">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="32b37-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="32b37-452">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="32b37-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="32b37-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="32b37-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="32b37-454">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="32b37-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="32b37-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="32b37-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="32b37-457">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="32b37-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="32b37-458">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="32b37-459">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="32b37-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="32b37-460">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="32b37-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="32b37-461">*appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="32b37-461">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="32b37-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="32b37-462">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="32b37-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="32b37-463">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="32b37-464">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="32b37-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="32b37-465">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="32b37-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="32b37-466">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="32b37-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="32b37-467">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="32b37-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="32b37-468">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="32b37-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="32b37-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="32b37-469">ConfigureServices</span></span>

<span data-ttu-id="32b37-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="32b37-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="32b37-472">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="32b37-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="32b37-473">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="32b37-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="32b37-474">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="32b37-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="32b37-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="32b37-475">ConfigureLogging</span></span>

<span data-ttu-id="32b37-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="32b37-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="32b37-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="32b37-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-478">UseConsoleLifetime</span></span>

<span data-ttu-id="32b37-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="32b37-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="32b37-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="32b37-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="32b37-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="32b37-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="32b37-482">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="32b37-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="32b37-483">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="32b37-483">Container configuration</span></span>

<span data-ttu-id="32b37-484">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="32b37-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="32b37-485">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="32b37-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="32b37-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="32b37-487">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="32b37-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="32b37-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="32b37-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="32b37-490">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="32b37-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="32b37-491">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="32b37-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="32b37-492">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="32b37-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="32b37-493">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="32b37-493">Extensibility</span></span>

<span data-ttu-id="32b37-494">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="32b37-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="32b37-495">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="32b37-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="32b37-496">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="32b37-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="32b37-497">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="32b37-497">Manage the host</span></span>

<span data-ttu-id="32b37-498">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="32b37-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="32b37-499">Executar</span><span class="sxs-lookup"><span data-stu-id="32b37-499">Run</span></span>

<span data-ttu-id="32b37-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="32b37-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="32b37-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-501">RunAsync</span></span>

<span data-ttu-id="32b37-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="32b37-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="32b37-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-503">RunConsoleAsync</span></span>

<span data-ttu-id="32b37-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="32b37-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="32b37-505">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-505">Start and StopAsync</span></span>

<span data-ttu-id="32b37-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="32b37-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="32b37-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="32b37-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="32b37-508">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="32b37-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="32b37-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="32b37-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="32b37-511">WaitForShutdown</span></span>

<span data-ttu-id="32b37-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>é disparado por meio do <xref:Microsoft.Extensions.Hosting.IHostLifetime> , como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta para <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="32b37-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="32b37-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="32b37-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="32b37-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="32b37-516">Controle externo</span><span class="sxs-lookup"><span data-stu-id="32b37-516">External control</span></span>

<span data-ttu-id="32b37-517">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="32b37-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="32b37-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="32b37-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="32b37-519">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="32b37-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="32b37-520">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="32b37-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="32b37-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="32b37-522">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="32b37-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="32b37-523">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="32b37-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="32b37-524">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="32b37-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="32b37-526">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="32b37-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="32b37-527">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="32b37-527">Cancellation Token</span></span> | <span data-ttu-id="32b37-528">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="32b37-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="32b37-529">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="32b37-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="32b37-530">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="32b37-531">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="32b37-531">All requests should be processed.</span></span> <span data-ttu-id="32b37-532">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="32b37-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="32b37-533">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="32b37-534">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="32b37-534">Requests may still be processing.</span></span> <span data-ttu-id="32b37-535">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="32b37-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="32b37-536">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="32b37-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="32b37-537">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="32b37-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="32b37-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="32b37-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="32b37-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="32b37-540">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="32b37-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="32b37-541">Os modelos de ASP.NET Core criam um host genérico do .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="32b37-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="32b37-542">Definição de host</span><span class="sxs-lookup"><span data-stu-id="32b37-542">Host definition</span></span>

<span data-ttu-id="32b37-543">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="32b37-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="32b37-544">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="32b37-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="32b37-545">Registro em log</span><span class="sxs-lookup"><span data-stu-id="32b37-545">Logging</span></span>
* <span data-ttu-id="32b37-546">Configuração</span><span class="sxs-lookup"><span data-stu-id="32b37-546">Configuration</span></span>
* <span data-ttu-id="32b37-547">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="32b37-547">`IHostedService` implementations</span></span>

<span data-ttu-id="32b37-548">Quando um host é iniciado, ele chama <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> registrado na coleção de serviços hospedados do contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="32b37-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="32b37-549">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="32b37-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="32b37-550">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="32b37-551">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="32b37-551">Set up a host</span></span>

<span data-ttu-id="32b37-552">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="32b37-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="32b37-553">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="32b37-553">The `Main` method:</span></span>

* <span data-ttu-id="32b37-554">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="32b37-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="32b37-555">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="32b37-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="32b37-556">Os modelos da Web ASP.NET Core geram o seguinte código para criar um host:</span><span class="sxs-lookup"><span data-stu-id="32b37-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="32b37-557">O código a seguir cria uma carga de trabalho não HTTP com uma `IHostedService` implementação adicionada ao contêiner de di.</span><span class="sxs-lookup"><span data-stu-id="32b37-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="32b37-558">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="32b37-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="32b37-559">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="32b37-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="32b37-560">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="32b37-561">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="32b37-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="32b37-562">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="32b37-562">Default builder settings</span></span>

<span data-ttu-id="32b37-563">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="32b37-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="32b37-564">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="32b37-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="32b37-565">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="32b37-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="32b37-566">Variáveis de ambiente prefixadas com `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="32b37-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="32b37-567">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="32b37-567">Command-line arguments.</span></span>
* <span data-ttu-id="32b37-568">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="32b37-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="32b37-569">*appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="32b37-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="32b37-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="32b37-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="32b37-571">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="32b37-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="32b37-572">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="32b37-572">Environment variables.</span></span>
  * <span data-ttu-id="32b37-573">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="32b37-573">Command-line arguments.</span></span>
* <span data-ttu-id="32b37-574">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="32b37-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="32b37-575">Console</span><span class="sxs-lookup"><span data-stu-id="32b37-575">Console</span></span>
  * <span data-ttu-id="32b37-576">Depurar</span><span class="sxs-lookup"><span data-stu-id="32b37-576">Debug</span></span>
  * <span data-ttu-id="32b37-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="32b37-577">EventSource</span></span>
  * <span data-ttu-id="32b37-578">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="32b37-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="32b37-579">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="32b37-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="32b37-580">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="32b37-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="32b37-581">Carrega a configuração de host de variáveis de ambiente prefixadas com `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="32b37-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="32b37-582">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="32b37-583">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="32b37-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="32b37-584">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="32b37-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="32b37-585">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true` .</span><span class="sxs-lookup"><span data-stu-id="32b37-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="32b37-586">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="32b37-586">Enables IIS integration.</span></span> <span data-ttu-id="32b37-587">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="32b37-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="32b37-588">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="32b37-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="32b37-589">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="32b37-589">Framework-provided services</span></span>

<span data-ttu-id="32b37-590">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="32b37-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="32b37-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="32b37-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="32b37-593">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="32b37-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="32b37-594">Para obter mais informações sobre serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="32b37-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="32b37-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="32b37-596">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="32b37-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="32b37-597">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="32b37-598">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="32b37-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="32b37-599">O exemplo a seguir é uma `IHostedService` implementação que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="32b37-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="32b37-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="32b37-600">IHostLifetime</span></span>

<span data-ttu-id="32b37-601">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="32b37-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="32b37-602">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="32b37-602">The last implementation registered is used.</span></span>

<span data-ttu-id="32b37-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="32b37-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="32b37-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="32b37-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="32b37-605">Escuta <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="32b37-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="32b37-606">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="32b37-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="32b37-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="32b37-607">IHostEnvironment</span></span>

<span data-ttu-id="32b37-608">Insira o <xref:Microsoft.Extensions.Hosting.IHostEnvironment> serviço em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="32b37-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="32b37-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="32b37-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="32b37-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="32b37-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="32b37-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="32b37-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="32b37-612">Os aplicativos Web implementam a `IWebHostEnvironment` interface, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="32b37-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="32b37-613">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="32b37-613">Host configuration</span></span>

<span data-ttu-id="32b37-614">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="32b37-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="32b37-615">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="32b37-616">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="32b37-617">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="32b37-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="32b37-618">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="32b37-619">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="32b37-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="32b37-620">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos pelo `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="32b37-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="32b37-621">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="32b37-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="32b37-622">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="32b37-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="32b37-623">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="32b37-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="32b37-624">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="32b37-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="32b37-625">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="32b37-625">App configuration</span></span>

<span data-ttu-id="32b37-626">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="32b37-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="32b37-627">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="32b37-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="32b37-628">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="32b37-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="32b37-629">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="32b37-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="32b37-630">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="32b37-631">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="32b37-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="32b37-632">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="32b37-632">Settings for all app types</span></span>

<span data-ttu-id="32b37-633">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="32b37-634">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="32b37-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="32b37-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="32b37-635">ApplicationName</span></span>

<span data-ttu-id="32b37-636">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="32b37-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="32b37-637">**Chave**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="32b37-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="32b37-638">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-638">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-639">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="32b37-640">**Variável de ambiente**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="32b37-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="32b37-641">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="32b37-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="32b37-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-642">ContentRoot</span></span>

<span data-ttu-id="32b37-643">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="32b37-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="32b37-644">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="32b37-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="32b37-645">**Chave**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="32b37-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="32b37-646">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-646">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-647">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="32b37-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="32b37-648">**Variável de ambiente**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="32b37-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="32b37-649">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="32b37-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="32b37-650">Para obter mais informações, confira:</span><span class="sxs-lookup"><span data-stu-id="32b37-650">For more information, see:</span></span>

* [<span data-ttu-id="32b37-651">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="32b37-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="32b37-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="32b37-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="32b37-653">EnvironmentName</span></span>

<span data-ttu-id="32b37-654">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="32b37-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="32b37-655">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="32b37-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="32b37-656">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="32b37-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="32b37-657">**Chave**:`environment`</span><span class="sxs-lookup"><span data-stu-id="32b37-657">**Key**: `environment`</span></span>  
<span data-ttu-id="32b37-658">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-658">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-659">**Padrão**:`Production`</span><span class="sxs-lookup"><span data-stu-id="32b37-659">**Default**: `Production`</span></span>  
<span data-ttu-id="32b37-660">**Variável de ambiente**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="32b37-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="32b37-661">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="32b37-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="32b37-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="32b37-662">ShutdownTimeout</span></span>

<span data-ttu-id="32b37-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="32b37-664">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="32b37-664">The default value is five seconds.</span></span>  <span data-ttu-id="32b37-665">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="32b37-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="32b37-666">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="32b37-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="32b37-667">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="32b37-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="32b37-668">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="32b37-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="32b37-669">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="32b37-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="32b37-670">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="32b37-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="32b37-671">**Chave**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="32b37-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="32b37-672">**Tipo**:`int`</span><span class="sxs-lookup"><span data-stu-id="32b37-672">**Type**: `int`</span></span>  
<span data-ttu-id="32b37-673">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="32b37-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="32b37-674">**Variável de ambiente**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="32b37-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="32b37-675">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="32b37-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="32b37-676">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="32b37-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="32b37-677">Desabilitar recarga de configuração de aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="32b37-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="32b37-678">Por [padrão](xref:fundamentals/configuration/index#default), *appsettings.jsem* e *appSettings. { Ambiente}. JSON* são recarregados quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="32b37-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="32b37-679">Para desabilitar esse comportamento de recarregamento no ASP.NET Core 5,0 Preview 3 ou posterior, defina a `hostBuilder:reloadConfigOnChange` chave como `false` .</span><span class="sxs-lookup"><span data-stu-id="32b37-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="32b37-680">**Chave**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="32b37-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="32b37-681">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-682">**Padrão**:`true`</span><span class="sxs-lookup"><span data-stu-id="32b37-682">**Default**: `true`</span></span>  
<span data-ttu-id="32b37-683">**Argumento de linha de comando**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="32b37-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="32b37-684">**Variável de ambiente**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="32b37-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="32b37-685">O separador de dois-pontos ( `:` ) não funciona com chaves hierárquicas de variáveis de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="32b37-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="32b37-686">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="32b37-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="32b37-687">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="32b37-687">Settings for web apps</span></span>

<span data-ttu-id="32b37-688">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="32b37-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="32b37-689">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="32b37-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="32b37-690">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="32b37-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="32b37-691">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="32b37-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="32b37-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="32b37-692">CaptureStartupErrors</span></span>

<span data-ttu-id="32b37-693">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="32b37-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="32b37-694">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="32b37-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="32b37-695">**Chave**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="32b37-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="32b37-696">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-697">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="32b37-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="32b37-698">**Variável de ambiente**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="32b37-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="32b37-699">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="32b37-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="32b37-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="32b37-700">DetailedErrors</span></span>

<span data-ttu-id="32b37-701">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="32b37-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="32b37-702">**Chave**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="32b37-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="32b37-703">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-704">**Padrão**:`false`</span><span class="sxs-lookup"><span data-stu-id="32b37-704">**Default**: `false`</span></span>  
<span data-ttu-id="32b37-705">**Variável de ambiente**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="32b37-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="32b37-706">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="32b37-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="32b37-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="32b37-708">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="32b37-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="32b37-709">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="32b37-710">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="32b37-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="32b37-711">**Chave**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="32b37-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="32b37-712">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-712">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-713">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="32b37-713">**Default**: Empty string</span></span>  
<span data-ttu-id="32b37-714">**Variável de ambiente**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="32b37-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="32b37-715">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="32b37-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="32b37-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="32b37-717">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="32b37-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="32b37-718">**Chave**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="32b37-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="32b37-719">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-719">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-720">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="32b37-720">**Default**: Empty string</span></span>  
<span data-ttu-id="32b37-721">**Variável de ambiente**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="32b37-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="32b37-722">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="32b37-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="32b37-723">HTTPS_Port</span></span>

<span data-ttu-id="32b37-724">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="32b37-724">The HTTPS redirect port.</span></span> <span data-ttu-id="32b37-725">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="32b37-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="32b37-726">**Chave**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="32b37-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="32b37-727">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-727">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-728">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="32b37-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="32b37-729">**Variável de ambiente**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="32b37-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="32b37-730">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="32b37-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="32b37-731">PreferHostingUrls</span></span>

<span data-ttu-id="32b37-732">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a `IServer` implementação.</span><span class="sxs-lookup"><span data-stu-id="32b37-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="32b37-733">**Chave**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="32b37-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="32b37-734">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-735">**Padrão**:`true`</span><span class="sxs-lookup"><span data-stu-id="32b37-735">**Default**: `true`</span></span>  
<span data-ttu-id="32b37-736">**Variável de ambiente**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="32b37-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="32b37-737">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="32b37-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="32b37-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="32b37-738">PreventHostingStartup</span></span>

<span data-ttu-id="32b37-739">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="32b37-740">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="32b37-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="32b37-741">**Chave**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="32b37-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="32b37-742">**Tipo**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="32b37-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="32b37-743">**Padrão**:`false`</span><span class="sxs-lookup"><span data-stu-id="32b37-743">**Default**: `false`</span></span>  
<span data-ttu-id="32b37-744">**Variável de ambiente**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="32b37-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="32b37-745">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="32b37-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="32b37-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="32b37-746">StartupAssembly</span></span>

<span data-ttu-id="32b37-747">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="32b37-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="32b37-748">**Chave**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="32b37-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="32b37-749">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-749">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-750">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="32b37-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="32b37-751">**Variável de ambiente**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="32b37-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="32b37-752">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="32b37-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="32b37-753">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="32b37-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="32b37-754">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="32b37-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="32b37-755">URLs</span><span class="sxs-lookup"><span data-stu-id="32b37-755">URLs</span></span>

<span data-ttu-id="32b37-756">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="32b37-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="32b37-757">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="32b37-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="32b37-758">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="32b37-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="32b37-759">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="32b37-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="32b37-760">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="32b37-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="32b37-761">**Chave**:`urls`</span><span class="sxs-lookup"><span data-stu-id="32b37-761">**Key**: `urls`</span></span>  
<span data-ttu-id="32b37-762">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-762">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-763">**Padrão**: `http://localhost:5000` e`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="32b37-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="32b37-764">**Variável de ambiente**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="32b37-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="32b37-765">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="32b37-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="32b37-766">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="32b37-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="32b37-767">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="32b37-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="32b37-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-768">WebRoot</span></span>

<span data-ttu-id="32b37-769">A propriedade [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) determina o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="32b37-770">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="32b37-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="32b37-771">**Chave**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="32b37-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="32b37-772">**Tipo**:`string`</span><span class="sxs-lookup"><span data-stu-id="32b37-772">**Type**: `string`</span></span>  
<span data-ttu-id="32b37-773">**Padrão**: o padrão é `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="32b37-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="32b37-774">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="32b37-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="32b37-775">**Variável de ambiente**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="32b37-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="32b37-776">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot` em `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="32b37-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="32b37-777">Para obter mais informações, confira:</span><span class="sxs-lookup"><span data-stu-id="32b37-777">For more information, see:</span></span>

* [<span data-ttu-id="32b37-778">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="32b37-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="32b37-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="32b37-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="32b37-780">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="32b37-780">Manage the host lifetime</span></span>

<span data-ttu-id="32b37-781">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="32b37-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="32b37-782">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="32b37-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="32b37-783">Executar</span><span class="sxs-lookup"><span data-stu-id="32b37-783">Run</span></span>

<span data-ttu-id="32b37-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="32b37-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="32b37-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-785">RunAsync</span></span>

<span data-ttu-id="32b37-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="32b37-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="32b37-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-787">RunConsoleAsync</span></span>

<span data-ttu-id="32b37-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>habilita o suporte a console, compila e inicia o host e aguarda a conclusão da <kbd>tecla CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM para desligar.</span><span class="sxs-lookup"><span data-stu-id="32b37-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="32b37-789">Iniciar</span><span class="sxs-lookup"><span data-stu-id="32b37-789">Start</span></span>

<span data-ttu-id="32b37-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="32b37-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="32b37-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-791">StartAsync</span></span>

<span data-ttu-id="32b37-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="32b37-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="32b37-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="32b37-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="32b37-794">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="32b37-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="32b37-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-795">StopAsync</span></span>

<span data-ttu-id="32b37-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="32b37-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="32b37-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="32b37-797">WaitForShutdown</span></span>

<span data-ttu-id="32b37-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="32b37-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="32b37-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="32b37-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="32b37-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="32b37-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="32b37-801">Controle externo</span><span class="sxs-lookup"><span data-stu-id="32b37-801">External control</span></span>

<span data-ttu-id="32b37-802">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="32b37-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="32b37-803">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="32b37-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
