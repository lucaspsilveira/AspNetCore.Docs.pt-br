---
title: Como fazer registro em log no .NET Core e no ASP.NET Core
author: rick-anderson
description: Saiba como usar a estrutura de registro em log fornecida pelo pacote do NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440734"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="af153-103">Como fazer registro em log no .NET Core e no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af153-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af153-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="af153-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="af153-105">O .NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="af153-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="af153-106">Este artigo mostra como usar a API de registro em log com provedores internos.</span><span class="sxs-lookup"><span data-stu-id="af153-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="af153-107">A maioria dos exemplos de código mostrados neste artigo é de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="af153-108">As partes específicas de registro desses trechos de código se aplicam a qualquer aplicativo .NET Core que use o [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="af153-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="af153-109">Para obter um exemplo de como usar o Host genérico em um aplicativo de console não web, consulte o arquivo *Program.cs* do aplicativo de [exemplo Tarefas em Segundo Plano](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ().<xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="af153-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="af153-110">O código de registro em log de aplicativos sem host genérico difere na maneira como os [provedores são adicionados](#add-providers) e como os [agentes são criados](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="af153-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="af153-111">Exemplos de código não host são mostrados nessas seções do artigo.</span><span class="sxs-lookup"><span data-stu-id="af153-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="af153-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af153-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="af153-113">Adicionar provedores</span><span class="sxs-lookup"><span data-stu-id="af153-113">Add providers</span></span>

<span data-ttu-id="af153-114">Um provedor de log exibe ou armazena logs.</span><span class="sxs-lookup"><span data-stu-id="af153-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="af153-115">Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="af153-116">Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="af153-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="af153-117">Para adicionar um provedor em um aplicativo que usa um host genérico, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="af153-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="af153-118">Em um aplicativo de console não host, chame o método de extensão `Add{provider name}` do provedor ao criar um `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="af153-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="af153-119">`LoggerFactory` e `AddConsole` exigem uma instrução `using` para `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="af153-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="af153-120">Os modelos de projeto padrão do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="af153-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="af153-121">Console</span><span class="sxs-lookup"><span data-stu-id="af153-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="af153-122">Depuração</span><span class="sxs-lookup"><span data-stu-id="af153-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="af153-123">Eventsource</span><span class="sxs-lookup"><span data-stu-id="af153-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="af153-124">[EventLog](#windows-eventlog-provider) (somente quando estiver em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="af153-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="af153-125">Você pode substituir os provedores padrão por aqueles de sua preferência.</span><span class="sxs-lookup"><span data-stu-id="af153-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="af153-126">Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.</span><span class="sxs-lookup"><span data-stu-id="af153-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="af153-127">Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.</span><span class="sxs-lookup"><span data-stu-id="af153-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="af153-128">Criar logs</span><span class="sxs-lookup"><span data-stu-id="af153-128">Create logs</span></span>

<span data-ttu-id="af153-129">Para criar logs, use um objeto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="af153-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="af153-130">Em um aplicativo Web ou serviço hospedado, obtenha um `ILogger` da DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="af153-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="af153-131">Em aplicativos de console não host, use o `LoggerFactory` para criar um `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="af153-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="af153-132">O exemplo do ASP.NET Core a seguir cria um agente de categoria `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="af153-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="af153-133">A *categoria* do log é uma cadeia de caracteres associada a cada log.</span><span class="sxs-lookup"><span data-stu-id="af153-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="af153-134">A instância de `ILogger<T>` cria logs que têm como a categoria o nome totalmente qualificado do tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="af153-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="af153-135">O exemplo do aplicativo de console não host a seguir cria um agente de categoria `LoggingConsoleApp.Program`.</span><span class="sxs-lookup"><span data-stu-id="af153-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="af153-136">Nos exemplos de ASP.NET Core e aplicativo de console a seguir, o agente é usado para criar logs de nível `Information`.</span><span class="sxs-lookup"><span data-stu-id="af153-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="af153-137">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="af153-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="af153-138">[Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="af153-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="af153-139">Criar logs na classe Programa</span><span class="sxs-lookup"><span data-stu-id="af153-139">Create logs in the Program class</span></span>

<span data-ttu-id="af153-140">Para gravar logs na classe `Program` de um aplicativo ASP.NET Core, obtenha uma instância de `ILogger` por meio da DI após compilar o host:</span><span class="sxs-lookup"><span data-stu-id="af153-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="af153-141">O registro durante a construção do host não é suportado diretamente.</span><span class="sxs-lookup"><span data-stu-id="af153-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="af153-142">No entanto, um madeireiro separado pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="af153-142">However, a separate logger can be used.</span></span> <span data-ttu-id="af153-143">No exemplo a seguir, um [logger Serilog](https://serilog.net/) é usado para fazer login `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="af153-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="af153-144">`AddSerilog`usa a configuração `Log.Logger`estática especificada em:</span><span class="sxs-lookup"><span data-stu-id="af153-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="af153-145">Criar logs na classe Startup</span><span class="sxs-lookup"><span data-stu-id="af153-145">Create logs in the Startup class</span></span>

<span data-ttu-id="af153-146">Para gravar logs no método `Startup.Configure` de um aplicativo ASP.NET Core, inclua um parâmetro `ILogger` na assinatura do método:</span><span class="sxs-lookup"><span data-stu-id="af153-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="af153-147">A gravação de logs antes da conclusão da configuração do contêiner de DI no método `Startup.ConfigureServices` não é uma ação compatível:</span><span class="sxs-lookup"><span data-stu-id="af153-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="af153-148">A injeção de agente no construtor `Startup` não é uma ação compatível.</span><span class="sxs-lookup"><span data-stu-id="af153-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="af153-149">A injeção de agente na assinatura do método `Startup.ConfigureServices` não é uma ação compatível</span><span class="sxs-lookup"><span data-stu-id="af153-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="af153-150">O motivo para essa restrição é que o registro em log depende da DI e da configuração, a qual por sua vez depende da DI.</span><span class="sxs-lookup"><span data-stu-id="af153-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="af153-151">O contêiner de DI não é configurado até `ConfigureServices` ser concluído.</span><span class="sxs-lookup"><span data-stu-id="af153-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="af153-152">A injeção de construtor de um agente em `Startup` funciona em versões anteriores do ASP.NET Core porque um contêiner de DI separado é criado para o host da Web.</span><span class="sxs-lookup"><span data-stu-id="af153-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="af153-153">Para obter informações sobre por que apenas um contêiner é criado para o host genérico, consulte o [anúncio de alteração da falha](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="af153-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="af153-154">Se precisar configurar um serviço que dependa de `ILogger<T>`, você ainda poderá fazer isso usando a injeção de construtor ou fornecendo um método de fábrica.</span><span class="sxs-lookup"><span data-stu-id="af153-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="af153-155">A abordagem do método de fábrica é recomendada somente se não há nenhuma outra opção.</span><span class="sxs-lookup"><span data-stu-id="af153-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="af153-156">Por exemplo, suponha que você precise preencher uma propriedade com um serviço da DI:</span><span class="sxs-lookup"><span data-stu-id="af153-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="af153-157">O código realçado anterior é um `Func` que é executado na primeira vez que o contêiner de DI precisa construir uma instância de `MyService`.</span><span class="sxs-lookup"><span data-stu-id="af153-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="af153-158">Você pode acessar qualquer um dos serviços registrados dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="af153-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="af153-159">Sem métodos de agente assíncronos</span><span class="sxs-lookup"><span data-stu-id="af153-159">No asynchronous logger methods</span></span>

<span data-ttu-id="af153-160">O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="af153-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="af153-161">Se o armazenamento de dados em log estiver lento, não grave diretamente nele.</span><span class="sxs-lookup"><span data-stu-id="af153-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="af153-162">Grave as mensagens de log em um repositório rápido primeiro e, depois, mova-as para um repositório lento.</span><span class="sxs-lookup"><span data-stu-id="af153-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="af153-163">Por exemplo, se você estiver enviado logs para o SQL Server, convém não fazer isso diretamente em um método `Log`, uma vez que os métodos `Log` são síncronos.</span><span class="sxs-lookup"><span data-stu-id="af153-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="af153-164">Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="af153-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="af153-165">Para obter mais informações, consulte este problema [do](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub.</span><span class="sxs-lookup"><span data-stu-id="af153-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="af153-166">Configuração</span><span class="sxs-lookup"><span data-stu-id="af153-166">Configuration</span></span>

<span data-ttu-id="af153-167">A configuração do provedor de logs é fornecida por um ou mais provedores de sincronização:</span><span class="sxs-lookup"><span data-stu-id="af153-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="af153-168">Formatos de arquivo (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="af153-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="af153-169">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="af153-169">Command-line arguments.</span></span>
* <span data-ttu-id="af153-170">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="af153-170">Environment variables.</span></span>
* <span data-ttu-id="af153-171">Objetos do .NET na memória.</span><span class="sxs-lookup"><span data-stu-id="af153-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="af153-172">O armazenamento do [Secret Manager](xref:security/app-secrets) não criptografado.</span><span class="sxs-lookup"><span data-stu-id="af153-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="af153-173">Um repositório de usuário criptografado, como o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="af153-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="af153-174">Provedores personalizados (instalados ou criados).</span><span class="sxs-lookup"><span data-stu-id="af153-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="af153-175">Por exemplo, a configuração de log geralmente é fornecida pela seção `Logging` dos arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="af153-176">O exemplo a seguir mostra o conteúdo de um típico arquivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="af153-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="af153-177">A propriedade `Logging` pode ter `LogLevel` e propriedades do provedor de logs (o Console é mostrado).</span><span class="sxs-lookup"><span data-stu-id="af153-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="af153-178">A propriedade `LogLevel` em `Logging` especifica o [nível](#log-level) mínimo para log nas categorias selecionadas.</span><span class="sxs-lookup"><span data-stu-id="af153-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="af153-179">No exemplo, as categorias `System` e `Microsoft` têm log no nível `Information`, e todas as outras no nível `Debug`.</span><span class="sxs-lookup"><span data-stu-id="af153-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="af153-180">Outras propriedades em `Logging` especificam provedores de logs.</span><span class="sxs-lookup"><span data-stu-id="af153-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="af153-181">O exemplo se refere ao provedor de Console.</span><span class="sxs-lookup"><span data-stu-id="af153-181">The example is for the Console provider.</span></span> <span data-ttu-id="af153-182">Se um provedor suportar [escopos de log,](#log-scopes) `IncludeScopes` indicará se eles estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="af153-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="af153-183">Uma propriedade de provedor (como `Console`, no exemplo) também pode especificar uma propriedade `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="af153-184">`LogLevel` em um provedor especifica os níveis de log para esse provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="af153-185">Se os níveis forem especificados em `Logging.{providername}.LogLevel`, eles substituirão o que estiver definido em `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="af153-186">A API de registro não inclui um cenário para alterar os níveis de log enquanto um aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="af153-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="af153-187">No entanto, alguns provedores de configuração são capazes de recarregar a configuração, o que tem efeito imediato na configuração de registro.</span><span class="sxs-lookup"><span data-stu-id="af153-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="af153-188">Por exemplo, o [Provedor de Configuração de Arquivos](xref:fundamentals/configuration/index#file-configuration-provider), que é adicionado `CreateDefaultBuilder` para ler arquivos de configurações, recarrega a configuração de registro por padrão.</span><span class="sxs-lookup"><span data-stu-id="af153-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="af153-189">Se a configuração for alterada em código enquanto um aplicativo estiver em execução, o aplicativo poderá chamar [iConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para atualizar a configuração de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="af153-190">Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="af153-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="af153-191">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="af153-191">Sample logging output</span></span>

<span data-ttu-id="af153-192">Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando.</span><span class="sxs-lookup"><span data-stu-id="af153-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="af153-193">Aqui está um exemplo da saída do console:</span><span class="sxs-lookup"><span data-stu-id="af153-193">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="af153-194">Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="af153-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="af153-195">Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af153-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="af153-196">Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="af153-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="af153-197">Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="af153-198">O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.</span><span class="sxs-lookup"><span data-stu-id="af153-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="af153-199">O restante deste artigo explica alguns detalhes e opções para registro em log.</span><span class="sxs-lookup"><span data-stu-id="af153-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="af153-200">Pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="af153-200">NuGet packages</span></span>

<span data-ttu-id="af153-201">As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="af153-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="af153-202">Categoria do log</span><span class="sxs-lookup"><span data-stu-id="af153-202">Log category</span></span>

<span data-ttu-id="af153-203">Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele.</span><span class="sxs-lookup"><span data-stu-id="af153-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="af153-204">Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="af153-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="af153-205">A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="af153-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="af153-206">Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:</span><span class="sxs-lookup"><span data-stu-id="af153-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="af153-207">Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="af153-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="af153-208">`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.</span><span class="sxs-lookup"><span data-stu-id="af153-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="af153-209">Nível de log</span><span class="sxs-lookup"><span data-stu-id="af153-209">Log level</span></span>

<span data-ttu-id="af153-210">Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="af153-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="af153-211">O nível de log indica a gravidade ou importância.</span><span class="sxs-lookup"><span data-stu-id="af153-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="af153-212">Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="af153-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="af153-213">O código a seguir cria os logs `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="af153-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="af153-214">No código anterior, o primeiro parâmetro é a [ID de evento de log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="af153-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="af153-215">O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes.</span><span class="sxs-lookup"><span data-stu-id="af153-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="af153-216">Os parâmetros de método serão explicados com posteriormente neste artigo, na [seção de modelos de mensagem](#log-message-template).</span><span class="sxs-lookup"><span data-stu-id="af153-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="af153-217">Os métodos de log que incluem o nível no nome do método (por exemplo, `LogInformation` e `LogWarning`) são [métodos de extensão para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="af153-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="af153-218">Esses métodos chamam um método `Log` que recebe um parâmetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="af153-219">Você pode chamar o método `Log` diretamente em vez de um desses métodos de extensão, mas a sintaxe é relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="af153-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="af153-220">Para saber mais, veja <xref:Microsoft.Extensions.Logging.ILogger> e o [código-fonte de extensões de agente](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="af153-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="af153-221">O ASP.NET Core define os seguintes níveis de log, ordenados aqui da menor para a maior gravidade.</span><span class="sxs-lookup"><span data-stu-id="af153-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="af153-222">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="af153-222">Trace = 0</span></span>

  <span data-ttu-id="af153-223">Para obter informações que normalmente são valiosas somente para depuração.</span><span class="sxs-lookup"><span data-stu-id="af153-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="af153-224">Essas mensagens podem conter dados confidenciais de aplicativos e, portanto, não devem ser habilitadas em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="af153-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="af153-225">*Desabilitado por padrão.*</span><span class="sxs-lookup"><span data-stu-id="af153-225">*Disabled by default.*</span></span>

* <span data-ttu-id="af153-226">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="af153-226">Debug = 1</span></span>

  <span data-ttu-id="af153-227">Para obter informações que possam ser úteis durante o desenvolvimento e a depuração.</span><span class="sxs-lookup"><span data-stu-id="af153-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="af153-228">Exemplo: `Entering method Configure with flag set to true.` habilite logs de nível `Debug` em produção somente ao solucionar problemas, devido ao alto volume de logs.</span><span class="sxs-lookup"><span data-stu-id="af153-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="af153-229">Information = 2</span><span class="sxs-lookup"><span data-stu-id="af153-229">Information = 2</span></span>

  <span data-ttu-id="af153-230">Para rastrear o fluxo geral do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="af153-231">Esses logs normalmente têm algum valor a longo prazo.</span><span class="sxs-lookup"><span data-stu-id="af153-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="af153-232">Exemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="af153-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="af153-233">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="af153-233">Warning = 3</span></span>

  <span data-ttu-id="af153-234">Para eventos anormais ou inesperados no fluxo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="af153-235">Eles podem incluir erros ou outras condições que não fazem com que o aplicativo pare, mas que talvez precisem ser investigados.</span><span class="sxs-lookup"><span data-stu-id="af153-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="af153-236">Exceções manipuladas são um local comum para usar o nível de log `Warning`.</span><span class="sxs-lookup"><span data-stu-id="af153-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="af153-237">Exemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="af153-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="af153-238">Error = 4</span><span class="sxs-lookup"><span data-stu-id="af153-238">Error = 4</span></span>

  <span data-ttu-id="af153-239">Para erros e exceções que não podem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="af153-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="af153-240">Essas mensagens indicam uma falha na atividade ou na operação atual (como a solicitação HTTP atual) e não uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="af153-241">Mensagem de log de exemplo:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="af153-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="af153-242">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="af153-242">Critical = 5</span></span>

  <span data-ttu-id="af153-243">Para falhas que exigem atenção imediata.</span><span class="sxs-lookup"><span data-stu-id="af153-243">For failures that require immediate attention.</span></span> <span data-ttu-id="af153-244">Exemplos: cenários de perda de dados, espaço em disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="af153-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="af153-245">Use o nível de log para controlar a quantidade de saída de log que é gravada em uma mídia de armazenamento específica ou em uma janela de exibição.</span><span class="sxs-lookup"><span data-stu-id="af153-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="af153-246">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af153-246">For example:</span></span>

* <span data-ttu-id="af153-247">Em produção:</span><span class="sxs-lookup"><span data-stu-id="af153-247">In production:</span></span>
  * <span data-ttu-id="af153-248">O registro `Trace` `Information` nos níveis de passagem produz um alto volume de mensagens de log detalhadas.</span><span class="sxs-lookup"><span data-stu-id="af153-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="af153-249">Para controlar custos e não exceder `Trace` `Information` os limites de armazenamento de dados, faça login através de mensagens de nível para um armazenamento de dados de alto volume e baixo custo.</span><span class="sxs-lookup"><span data-stu-id="af153-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="af153-250">O `Warning` registro `Critical` em níveis tipicamente produz menos mensagens de registro menores.</span><span class="sxs-lookup"><span data-stu-id="af153-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="af153-251">Portanto, custos e limites de armazenamento geralmente não são uma preocupação, o que resulta em maior flexibilidade na escolha do armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="af153-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="af153-252">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="af153-252">During development:</span></span>
  * <span data-ttu-id="af153-253">Faça `Warning` `Critical` login através de mensagens para o console.</span><span class="sxs-lookup"><span data-stu-id="af153-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="af153-254">Adicione `Trace` `Information` mensagens ao solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="af153-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="af153-255">A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.</span><span class="sxs-lookup"><span data-stu-id="af153-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="af153-256">O ASP.NET Core grava logs para eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="af153-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="af153-257">Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados.</span><span class="sxs-lookup"><span data-stu-id="af153-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="af153-258">Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:</span><span class="sxs-lookup"><span data-stu-id="af153-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="af153-259">ID de evento de log</span><span class="sxs-lookup"><span data-stu-id="af153-259">Log event ID</span></span>

<span data-ttu-id="af153-260">Cada log pode especificar uma *ID do evento*.</span><span class="sxs-lookup"><span data-stu-id="af153-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="af153-261">O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:</span><span class="sxs-lookup"><span data-stu-id="af153-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="af153-262">Uma ID de evento associa um conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="af153-262">An event ID associates a set of events.</span></span> <span data-ttu-id="af153-263">Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.</span><span class="sxs-lookup"><span data-stu-id="af153-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="af153-264">O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar.</span><span class="sxs-lookup"><span data-stu-id="af153-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="af153-265">O provedor de Depuração não mostra IDs de eventos.</span><span class="sxs-lookup"><span data-stu-id="af153-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="af153-266">O provedor de console mostra IDs de evento entre colchetes após a categoria:</span><span class="sxs-lookup"><span data-stu-id="af153-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="af153-267">Modelo de mensagem de log</span><span class="sxs-lookup"><span data-stu-id="af153-267">Log message template</span></span>

<span data-ttu-id="af153-268">Cada log especifica um modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="af153-268">Each log specifies a message template.</span></span> <span data-ttu-id="af153-269">O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos.</span><span class="sxs-lookup"><span data-stu-id="af153-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="af153-270">Use nomes para os espaços reservados, não números.</span><span class="sxs-lookup"><span data-stu-id="af153-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="af153-271">A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores.</span><span class="sxs-lookup"><span data-stu-id="af153-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="af153-272">No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:</span><span class="sxs-lookup"><span data-stu-id="af153-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="af153-273">Esse código cria uma mensagem de log com os valores de parâmetro na sequência:</span><span class="sxs-lookup"><span data-stu-id="af153-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="af153-274">A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="af153-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="af153-275">Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado.</span><span class="sxs-lookup"><span data-stu-id="af153-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="af153-276">Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos.</span><span class="sxs-lookup"><span data-stu-id="af153-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="af153-277">Por exemplo, suponha que as chamadas de método do agente sejam assim:</span><span class="sxs-lookup"><span data-stu-id="af153-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="af153-278">Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log.</span><span class="sxs-lookup"><span data-stu-id="af153-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="af153-279">Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.</span><span class="sxs-lookup"><span data-stu-id="af153-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="af153-280">Exceções de registro em log</span><span class="sxs-lookup"><span data-stu-id="af153-280">Logging exceptions</span></span>

<span data-ttu-id="af153-281">Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="af153-282">Provedores diferentes manipulam as informações de exceção de maneiras diferentes.</span><span class="sxs-lookup"><span data-stu-id="af153-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="af153-283">Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="af153-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="af153-284">Filtragem de linha</span><span class="sxs-lookup"><span data-stu-id="af153-284">Log filtering</span></span>

<span data-ttu-id="af153-285">Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias.</span><span class="sxs-lookup"><span data-stu-id="af153-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="af153-286">Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.</span><span class="sxs-lookup"><span data-stu-id="af153-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="af153-287">Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="af153-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="af153-288">O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="af153-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="af153-289">Criar regras de filtro na configuração</span><span class="sxs-lookup"><span data-stu-id="af153-289">Create filter rules in configuration</span></span>

<span data-ttu-id="af153-290">O código de `CreateDefaultBuilder` modelo de projeto chama para configurar o registro para os provedores Console, Debug e EventSource (ASP.NET Core 2.2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="af153-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="af153-291">O método `CreateDefaultBuilder` configura o registro em log para procurar a configuração em uma seção `Logging`, conforme explicado [anteriormente neste artigo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="af153-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="af153-292">Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="af153-293">Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores.</span><span class="sxs-lookup"><span data-stu-id="af153-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="af153-294">Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.</span><span class="sxs-lookup"><span data-stu-id="af153-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="af153-295">Regras de filtro no código</span><span class="sxs-lookup"><span data-stu-id="af153-295">Filter rules in code</span></span>

<span data-ttu-id="af153-296">O exemplo a seguir mostra como registrar regras de filtro no código:</span><span class="sxs-lookup"><span data-stu-id="af153-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="af153-297">O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo.</span><span class="sxs-lookup"><span data-stu-id="af153-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="af153-298">O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="af153-299">Como as regras de filtragem são aplicadas</span><span class="sxs-lookup"><span data-stu-id="af153-299">How filtering rules are applied</span></span>

<span data-ttu-id="af153-300">Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="af153-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="af153-301">As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.</span><span class="sxs-lookup"><span data-stu-id="af153-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="af153-302">Número</span><span class="sxs-lookup"><span data-stu-id="af153-302">Number</span></span> | <span data-ttu-id="af153-303">Provedor</span><span class="sxs-lookup"><span data-stu-id="af153-303">Provider</span></span>      | <span data-ttu-id="af153-304">Categorias que começam com...</span><span class="sxs-lookup"><span data-stu-id="af153-304">Categories that begin with ...</span></span>          | <span data-ttu-id="af153-305">Nível de log mínimo</span><span class="sxs-lookup"><span data-stu-id="af153-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="af153-306">1</span><span class="sxs-lookup"><span data-stu-id="af153-306">1</span></span>      | <span data-ttu-id="af153-307">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-307">Debug</span></span>         | <span data-ttu-id="af153-308">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="af153-308">All categories</span></span>                          | <span data-ttu-id="af153-309">Informações</span><span class="sxs-lookup"><span data-stu-id="af153-309">Information</span></span>       |
| <span data-ttu-id="af153-310">2</span><span class="sxs-lookup"><span data-stu-id="af153-310">2</span></span>      | <span data-ttu-id="af153-311">Console</span><span class="sxs-lookup"><span data-stu-id="af153-311">Console</span></span>       | <span data-ttu-id="af153-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="af153-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="af153-313">Aviso</span><span class="sxs-lookup"><span data-stu-id="af153-313">Warning</span></span>           |
| <span data-ttu-id="af153-314">3</span><span class="sxs-lookup"><span data-stu-id="af153-314">3</span></span>      | <span data-ttu-id="af153-315">Console</span><span class="sxs-lookup"><span data-stu-id="af153-315">Console</span></span>       | <span data-ttu-id="af153-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="af153-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="af153-317">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-317">Debug</span></span>             |
| <span data-ttu-id="af153-318">4</span><span class="sxs-lookup"><span data-stu-id="af153-318">4</span></span>      | <span data-ttu-id="af153-319">Console</span><span class="sxs-lookup"><span data-stu-id="af153-319">Console</span></span>       | <span data-ttu-id="af153-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="af153-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="af153-321">Erro</span><span class="sxs-lookup"><span data-stu-id="af153-321">Error</span></span>             |
| <span data-ttu-id="af153-322">5</span><span class="sxs-lookup"><span data-stu-id="af153-322">5</span></span>      | <span data-ttu-id="af153-323">Console</span><span class="sxs-lookup"><span data-stu-id="af153-323">Console</span></span>       | <span data-ttu-id="af153-324">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="af153-324">All categories</span></span>                          | <span data-ttu-id="af153-325">Informações</span><span class="sxs-lookup"><span data-stu-id="af153-325">Information</span></span>       |
| <span data-ttu-id="af153-326">6</span><span class="sxs-lookup"><span data-stu-id="af153-326">6</span></span>      | <span data-ttu-id="af153-327">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="af153-327">All providers</span></span> | <span data-ttu-id="af153-328">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="af153-328">All categories</span></span>                          | <span data-ttu-id="af153-329">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-329">Debug</span></span>             |
| <span data-ttu-id="af153-330">7</span><span class="sxs-lookup"><span data-stu-id="af153-330">7</span></span>      | <span data-ttu-id="af153-331">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="af153-331">All providers</span></span> | <span data-ttu-id="af153-332">Sistema</span><span class="sxs-lookup"><span data-stu-id="af153-332">System</span></span>                                  | <span data-ttu-id="af153-333">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-333">Debug</span></span>             |
| <span data-ttu-id="af153-334">8</span><span class="sxs-lookup"><span data-stu-id="af153-334">8</span></span>      | <span data-ttu-id="af153-335">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-335">Debug</span></span>         | <span data-ttu-id="af153-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="af153-336">Microsoft</span></span>                               | <span data-ttu-id="af153-337">Trace</span><span class="sxs-lookup"><span data-stu-id="af153-337">Trace</span></span>             |

<span data-ttu-id="af153-338">Quando um objeto `ILogger` é criado, o objeto `ILoggerFactory` seleciona uma única regra por provedor para aplicar a esse agente.</span><span class="sxs-lookup"><span data-stu-id="af153-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="af153-339">Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas.</span><span class="sxs-lookup"><span data-stu-id="af153-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="af153-340">A regra mais específica possível para cada par de categoria e provedor é selecionada dentre as regras disponíveis.</span><span class="sxs-lookup"><span data-stu-id="af153-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="af153-341">O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:</span><span class="sxs-lookup"><span data-stu-id="af153-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="af153-342">Selecione todas as regras que correspondem ao provedor ou seu alias.</span><span class="sxs-lookup"><span data-stu-id="af153-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="af153-343">Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.</span><span class="sxs-lookup"><span data-stu-id="af153-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="af153-344">Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência.</span><span class="sxs-lookup"><span data-stu-id="af153-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="af153-345">Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.</span><span class="sxs-lookup"><span data-stu-id="af153-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="af153-346">Se várias regras forem selecionadas, use a **última**.</span><span class="sxs-lookup"><span data-stu-id="af153-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="af153-347">Se nenhuma regra for selecionada, use `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="af153-348">Com a lista anterior de regras, suponha que você crie um objeto `ILogger` para a categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="af153-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="af153-349">Para o provedor Depuração as regras 1, 6 e 8 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="af153-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="af153-350">A regra 8 é mais específica, portanto é a que será selecionada.</span><span class="sxs-lookup"><span data-stu-id="af153-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="af153-351">Para o provedor Console as regras 3, 4, 5 e 6 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="af153-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="af153-352">A regra 3 é a mais específica.</span><span class="sxs-lookup"><span data-stu-id="af153-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="af153-353">A instância `ILogger` resultante envia logs de nível `Trace` e superior para o provedor Depuração.</span><span class="sxs-lookup"><span data-stu-id="af153-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="af153-354">Logs de nível `Debug` e superior são enviados para o provedor Console.</span><span class="sxs-lookup"><span data-stu-id="af153-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="af153-355">Aliases de provedor</span><span class="sxs-lookup"><span data-stu-id="af153-355">Provider aliases</span></span>

<span data-ttu-id="af153-356">Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="af153-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="af153-357">Para os provedores internos, use os seguintes aliases:</span><span class="sxs-lookup"><span data-stu-id="af153-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="af153-358">Console</span><span class="sxs-lookup"><span data-stu-id="af153-358">Console</span></span>
* <span data-ttu-id="af153-359">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-359">Debug</span></span>
* <span data-ttu-id="af153-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="af153-360">EventSource</span></span>
* <span data-ttu-id="af153-361">EventLog</span><span class="sxs-lookup"><span data-stu-id="af153-361">EventLog</span></span>
* <span data-ttu-id="af153-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="af153-362">TraceSource</span></span>
* <span data-ttu-id="af153-363">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="af153-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="af153-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="af153-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="af153-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="af153-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="af153-366">Nível mínimo padrão</span><span class="sxs-lookup"><span data-stu-id="af153-366">Default minimum level</span></span>

<span data-ttu-id="af153-367">Há uma configuração de nível mínimo que entra em vigor somente se nenhuma regra de código ou de configuração se aplicar a um provedor e uma categoria determinados.</span><span class="sxs-lookup"><span data-stu-id="af153-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="af153-368">O exemplo a seguir mostra como definir o nível mínimo:</span><span class="sxs-lookup"><span data-stu-id="af153-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="af153-369">Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="af153-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="af153-370">Funções de filtro</span><span class="sxs-lookup"><span data-stu-id="af153-370">Filter functions</span></span>

<span data-ttu-id="af153-371">Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código.</span><span class="sxs-lookup"><span data-stu-id="af153-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="af153-372">O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log.</span><span class="sxs-lookup"><span data-stu-id="af153-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="af153-373">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af153-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="af153-374">Categorias e níveis de sistema</span><span class="sxs-lookup"><span data-stu-id="af153-374">System categories and levels</span></span>

<span data-ttu-id="af153-375">Veja algumas categorias usadas pelo ASP.NET Core e Entity Framework Core, com anotações sobre quais logs esperar delas:</span><span class="sxs-lookup"><span data-stu-id="af153-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="af153-376">Categoria</span><span class="sxs-lookup"><span data-stu-id="af153-376">Category</span></span>                            | <span data-ttu-id="af153-377">Observações</span><span class="sxs-lookup"><span data-stu-id="af153-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="af153-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="af153-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="af153-379">Diagnóstico geral de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="af153-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="af153-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="af153-381">Quais chaves foram consideradas, encontradas e usadas.</span><span class="sxs-lookup"><span data-stu-id="af153-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="af153-382">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="af153-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="af153-383">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="af153-383">Hosts allowed.</span></span> |
| <span data-ttu-id="af153-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="af153-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="af153-385">Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas.</span><span class="sxs-lookup"><span data-stu-id="af153-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="af153-386">Quais assemblies de inicialização de hospedagem foram carregados.</span><span class="sxs-lookup"><span data-stu-id="af153-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="af153-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="af153-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="af153-388">Diagnóstico do MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="af153-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="af153-389">Model binding, execução de filtro, compilação de exibição, seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="af153-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="af153-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="af153-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="af153-391">Informações de correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="af153-391">Route matching information.</span></span> |
| <span data-ttu-id="af153-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="af153-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="af153-393">Respostas de início, parada e atividade da conexão.</span><span class="sxs-lookup"><span data-stu-id="af153-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="af153-394">Informações sobre o certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af153-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="af153-395">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="af153-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="af153-396">Arquivos atendidos.</span><span class="sxs-lookup"><span data-stu-id="af153-396">Files served.</span></span> |
| <span data-ttu-id="af153-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="af153-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="af153-398">Diagnóstico geral do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="af153-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="af153-399">Atividade e configuração do banco de dados, detecção de alterações, migrações.</span><span class="sxs-lookup"><span data-stu-id="af153-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="af153-400">Escopos de log</span><span class="sxs-lookup"><span data-stu-id="af153-400">Log scopes</span></span>

 <span data-ttu-id="af153-401">Um *escopo* pode agrupar um conjunto de operações lógicas.</span><span class="sxs-lookup"><span data-stu-id="af153-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="af153-402">Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto.</span><span class="sxs-lookup"><span data-stu-id="af153-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="af153-403">Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.</span><span class="sxs-lookup"><span data-stu-id="af153-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="af153-404">Um escopo é um tipo `IDisposable` retornado pelo método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e que dura até que seja descartado.</span><span class="sxs-lookup"><span data-stu-id="af153-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="af153-405">Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:</span><span class="sxs-lookup"><span data-stu-id="af153-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="af153-406">O código a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="af153-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="af153-407">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="af153-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="af153-408">A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.</span><span class="sxs-lookup"><span data-stu-id="af153-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="af153-409">Saiba mais sobre como configurar na seção [Configuração](#configuration).</span><span class="sxs-lookup"><span data-stu-id="af153-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="af153-410">Cada mensagem de log inclui as informações com escopo definido:</span><span class="sxs-lookup"><span data-stu-id="af153-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="af153-411">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="af153-411">Built-in logging providers</span></span>

<span data-ttu-id="af153-412">O ASP.NET Core vem com os seguintes provedores:</span><span class="sxs-lookup"><span data-stu-id="af153-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="af153-413">Console</span><span class="sxs-lookup"><span data-stu-id="af153-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="af153-414">Depuração</span><span class="sxs-lookup"><span data-stu-id="af153-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="af153-415">Eventsource</span><span class="sxs-lookup"><span data-stu-id="af153-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="af153-416">EventLog</span><span class="sxs-lookup"><span data-stu-id="af153-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="af153-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="af153-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="af153-418">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="af153-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="af153-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="af153-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="af153-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="af153-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="af153-421">Para obter informações sobre StdOut e o log de depuração com o Módulo do ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="af153-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="af153-422">Provedor do console</span><span class="sxs-lookup"><span data-stu-id="af153-422">Console provider</span></span>

<span data-ttu-id="af153-423">O pacote de provedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envia a saída de log para o console.</span><span class="sxs-lookup"><span data-stu-id="af153-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="af153-424">Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="af153-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="af153-425">Depurar provedor</span><span class="sxs-lookup"><span data-stu-id="af153-425">Debug provider</span></span>

<span data-ttu-id="af153-426">O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="af153-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="af153-427">No Linux, esse provedor grava logs em */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="af153-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="af153-428">Provedor de Origem de Eventos</span><span class="sxs-lookup"><span data-stu-id="af153-428">Event Source provider</span></span>

<span data-ttu-id="af153-429">O pacote [microsoft.extensions.logging.eventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) é escrito em uma plataforma `Microsoft-Extensions-Logging`multiplataforma de origem de evento com o nome .</span><span class="sxs-lookup"><span data-stu-id="af153-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="af153-430">No Windows, o provedor usa [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="af153-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="af153-431">O provedor de origem de `CreateDefaultBuilder` eventos é adicionado automaticamente quando é chamado para construir o host.</span><span class="sxs-lookup"><span data-stu-id="af153-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="af153-432">ferramentade rastreamento dotnet</span><span class="sxs-lookup"><span data-stu-id="af153-432">dotnet trace tooling</span></span>

<span data-ttu-id="af153-433">A ferramenta [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) é uma ferramenta global CLI multiplataforma que permite a coleta de traços .NET Core de um processo em execução.</span><span class="sxs-lookup"><span data-stu-id="af153-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="af153-434">A ferramenta <xref:Microsoft.Extensions.Logging.EventSource> coleta dados <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>do provedor usando um .</span><span class="sxs-lookup"><span data-stu-id="af153-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="af153-435">Instale a ferramenta de rastreamento dotnet com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="af153-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="af153-436">Use a ferramenta de rastreamento dotnet para coletar um rastreamento de um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="af153-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="af153-437">Se o aplicativo não construir `CreateDefaultBuilder`o host com , adicione o [provedor de Origem de Eventos](#event-source-provider) à configuração de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="af153-438">Execute o aplicativo `dotnet run` com o comando.</span><span class="sxs-lookup"><span data-stu-id="af153-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="af153-439">Determine o identificador de processo (PID) do aplicativo .NET Core:</span><span class="sxs-lookup"><span data-stu-id="af153-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="af153-440">No Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="af153-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="af153-441">Gerenciador de tarefas (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="af153-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="af153-442">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="af153-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="af153-443">Comando Get-Process Powershell</span><span class="sxs-lookup"><span data-stu-id="af153-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="af153-444">No Linux, use o [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="af153-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="af153-445">Encontre o PID para o processo que tem o mesmo nome da montagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="af153-446">Execute `dotnet trace` o comando.</span><span class="sxs-lookup"><span data-stu-id="af153-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="af153-447">Sintaxe de comando geral:</span><span class="sxs-lookup"><span data-stu-id="af153-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="af153-448">Ao usar um shell de comando `--providers` PowerShell, inserte o valor em cotações únicas ():`'`</span><span class="sxs-lookup"><span data-stu-id="af153-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="af153-449">Em plataformas não-Windows, `-f speedscope` adicione a opção de alterar `speedscope`o formato do arquivo de rastreamento de saída para .</span><span class="sxs-lookup"><span data-stu-id="af153-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="af153-450">Palavra-chave</span><span class="sxs-lookup"><span data-stu-id="af153-450">Keyword</span></span> | <span data-ttu-id="af153-451">Descrição</span><span class="sxs-lookup"><span data-stu-id="af153-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="af153-452">1</span><span class="sxs-lookup"><span data-stu-id="af153-452">1</span></span>       | <span data-ttu-id="af153-453">Log metaeventos `LoggingEventSource`sobre o .</span><span class="sxs-lookup"><span data-stu-id="af153-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="af153-454">Não registra eventos `ILogger`de ).</span><span class="sxs-lookup"><span data-stu-id="af153-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="af153-455">2</span><span class="sxs-lookup"><span data-stu-id="af153-455">2</span></span>       | <span data-ttu-id="af153-456">Liga o `Message` evento `ILogger.Log()` quando é chamado.</span><span class="sxs-lookup"><span data-stu-id="af153-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="af153-457">Fornece informações de forma programática (não formatada).</span><span class="sxs-lookup"><span data-stu-id="af153-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="af153-458">4</span><span class="sxs-lookup"><span data-stu-id="af153-458">4</span></span>       | <span data-ttu-id="af153-459">Liga o `FormatMessage` evento `ILogger.Log()` quando é chamado.</span><span class="sxs-lookup"><span data-stu-id="af153-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="af153-460">Fornece a versão de string formatado das informações.</span><span class="sxs-lookup"><span data-stu-id="af153-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="af153-461">8</span><span class="sxs-lookup"><span data-stu-id="af153-461">8</span></span>       | <span data-ttu-id="af153-462">Liga o `MessageJson` evento `ILogger.Log()` quando é chamado.</span><span class="sxs-lookup"><span data-stu-id="af153-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="af153-463">Fornece uma representação JSON dos argumentos.</span><span class="sxs-lookup"><span data-stu-id="af153-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="af153-464">Evento em nível</span><span class="sxs-lookup"><span data-stu-id="af153-464">Event Level</span></span> | <span data-ttu-id="af153-465">Descrição</span><span class="sxs-lookup"><span data-stu-id="af153-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="af153-466">0</span><span class="sxs-lookup"><span data-stu-id="af153-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="af153-467">1</span><span class="sxs-lookup"><span data-stu-id="af153-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="af153-468">2</span><span class="sxs-lookup"><span data-stu-id="af153-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="af153-469">3</span><span class="sxs-lookup"><span data-stu-id="af153-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="af153-470">4</span><span class="sxs-lookup"><span data-stu-id="af153-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="af153-471">5</span><span class="sxs-lookup"><span data-stu-id="af153-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="af153-472">`FilterSpecs`entradas `{Logger Category}` para `{Event Level}` e representam condições adicionais de filtragem de log.</span><span class="sxs-lookup"><span data-stu-id="af153-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="af153-473">Entradas separadas `FilterSpecs` com`;`ponto e vírgula ( ).</span><span class="sxs-lookup"><span data-stu-id="af153-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="af153-474">Exemplo usando um shell de comando do `--providers` Windows **(sem** cotações únicas em torno do valor):</span><span class="sxs-lookup"><span data-stu-id="af153-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="af153-475">O comando anterior ativa:</span><span class="sxs-lookup"><span data-stu-id="af153-475">The preceding command activates:</span></span>

   * <span data-ttu-id="af153-476">O logger Fonte de Evento para`4`produzir strings formatadas ( ) para erros (`2`).</span><span class="sxs-lookup"><span data-stu-id="af153-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="af153-477">`Microsoft.AspNetCore.Hosting`registro no `Informational` nível`4`de registro ().</span><span class="sxs-lookup"><span data-stu-id="af153-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="af153-478">Interrompa a ferramenta de rastreamento dotnet pressionando a tecla Enter ou Ctrl+C.</span><span class="sxs-lookup"><span data-stu-id="af153-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="af153-479">O rastreamento é salvo com o nome *trace.nettrace* na pasta onde o `dotnet trace` comando é executado.</span><span class="sxs-lookup"><span data-stu-id="af153-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="af153-480">Abra o rastreamento com [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="af153-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="af153-481">Abra o arquivo *trace.nettrace* e explore os eventos de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="af153-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="af153-482">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="af153-482">For more information, see:</span></span>

* <span data-ttu-id="af153-483">[Rastreamento para utilitário de análise de desempenho (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.documentação do Núcleo NET)</span><span class="sxs-lookup"><span data-stu-id="af153-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="af153-484">[Rastreamento para utilitário de análise de desempenho (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentação dotnet/diagnostics GitHub repositório)</span><span class="sxs-lookup"><span data-stu-id="af153-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="af153-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (navegador API.NET)</span><span class="sxs-lookup"><span data-stu-id="af153-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="af153-486">[LoggingEventFonte de referência (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Para obter a fonte de `release/{Version}`referência `{Version}` para uma versão diferente, altere o ramo para , onde está a versão do ASP.NET Core desejada.</span><span class="sxs-lookup"><span data-stu-id="af153-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="af153-487">[Perfview](#perfview) &ndash; Útil para visualizar traços da Fonte do Evento.</span><span class="sxs-lookup"><span data-stu-id="af153-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="af153-488">Perfview</span><span class="sxs-lookup"><span data-stu-id="af153-488">Perfview</span></span>

<span data-ttu-id="af153-489">Use o [utilitário PerfView](https://github.com/Microsoft/perfview) para coletar e exibir logs.</span><span class="sxs-lookup"><span data-stu-id="af153-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="af153-490">Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="af153-491">Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**.</span><span class="sxs-lookup"><span data-stu-id="af153-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="af153-492">(Não se esqueça do asterisco no início da cadeia de caracteres).</span><span class="sxs-lookup"><span data-stu-id="af153-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="af153-494">Provedor EventLog do Windows</span><span class="sxs-lookup"><span data-stu-id="af153-494">Windows EventLog provider</span></span>

<span data-ttu-id="af153-495">O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="af153-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="af153-496">As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="af153-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="af153-497">Se `null` especificado ou não, as seguintes configurações padrão serão usadas:</span><span class="sxs-lookup"><span data-stu-id="af153-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="af153-498">`LogName`&ndash; "Aplicação"</span><span class="sxs-lookup"><span data-stu-id="af153-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="af153-499">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="af153-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="af153-500">`MachineName` &ndash; computador local</span><span class="sxs-lookup"><span data-stu-id="af153-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="af153-501">Os eventos são registrados para [nível de aviso e superior .](#log-level)</span><span class="sxs-lookup"><span data-stu-id="af153-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="af153-502">Para registrar eventos `Warning`inferiores, defina explicitamente o nível de registro.</span><span class="sxs-lookup"><span data-stu-id="af153-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="af153-503">Por exemplo, adicione o seguinte ao arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="af153-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="af153-504">Provedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="af153-504">TraceSource provider</span></span>

<span data-ttu-id="af153-505">O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="af153-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="af153-506">As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="af153-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="af153-507">Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="af153-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="af153-508">O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="af153-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="af153-509">Provedor do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="af153-509">Azure App Service provider</span></span>

<span data-ttu-id="af153-510">O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="af153-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="af153-511">O pacote do provedor não está incluído na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="af153-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="af153-512">Para usar o provedor, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="af153-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="af153-513">Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="af153-514">Quando você implanta em um aplicativo do Serviço de Aplicativo, o aplicativo respeita as configurações na seção [Logs do Serviço de Aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) da página **Serviço de Aplicativo** do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="af153-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="af153-515">Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="af153-516">**Log de aplicativo (Sistema de Arquivos)**</span><span class="sxs-lookup"><span data-stu-id="af153-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="af153-517">**Log de aplicativo (Blob)**</span><span class="sxs-lookup"><span data-stu-id="af153-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="af153-518">O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="af153-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="af153-519">O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2.</span><span class="sxs-lookup"><span data-stu-id="af153-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="af153-520">O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="af153-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="af153-521">O provedor funciona somente quando o projeto é executado no ambiente do Azure.</span><span class="sxs-lookup"><span data-stu-id="af153-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="af153-522">Ele não tem nenhum efeito quando o projeto é executado localmente&mdash;ele não grava em arquivos locais ou no armazenamento de desenvolvimento local para blobs.</span><span class="sxs-lookup"><span data-stu-id="af153-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="af153-523">Fluxo de log do Azure</span><span class="sxs-lookup"><span data-stu-id="af153-523">Azure log streaming</span></span>

<span data-ttu-id="af153-524">O fluxo de log do Azure permite que você exiba a atividade de log em tempo real:</span><span class="sxs-lookup"><span data-stu-id="af153-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="af153-525">O servidor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="af153-525">The app server</span></span>
* <span data-ttu-id="af153-526">Do servidor Web</span><span class="sxs-lookup"><span data-stu-id="af153-526">The web server</span></span>
* <span data-ttu-id="af153-527">De uma solicitação de rastreio com falha</span><span class="sxs-lookup"><span data-stu-id="af153-527">Failed request tracing</span></span>

<span data-ttu-id="af153-528">Para configurar o fluxo de log do Azure:</span><span class="sxs-lookup"><span data-stu-id="af153-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="af153-529">Navegue até a página **Logs do Serviço de Aplicativo** da página do portal do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="af153-530">Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**.</span><span class="sxs-lookup"><span data-stu-id="af153-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="af153-531">Escolha o **Nível** de log.</span><span class="sxs-lookup"><span data-stu-id="af153-531">Choose the log **Level**.</span></span> <span data-ttu-id="af153-532">Essa configuração só se aplica ao streaming de log do Azure, não a outros provedores de registro no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="af153-533">Navegue até a página **Fluxo de Log** para exibir as mensagens de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="af153-534">Elas são registradas pelo aplicativo por meio da interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="af153-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="af153-535">Log de rastreamento do Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="af153-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="af153-536">O pacote de provedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava os logs no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="af153-537">O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria.</span><span class="sxs-lookup"><span data-stu-id="af153-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="af153-538">Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="af153-539">O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="af153-540">Se você usar esse pacote, não precisará instalar o pacote de provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="af153-541">Não use o pacote [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que é para o ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="af153-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="af153-542">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="af153-543">Visão geral do Application Insights</span><span class="sxs-lookup"><span data-stu-id="af153-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="af153-544">[Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.</span><span class="sxs-lookup"><span data-stu-id="af153-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="af153-545">[ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="af153-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs) (Adaptadores de registro em log do Application Insights).</span><span class="sxs-lookup"><span data-stu-id="af153-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="af153-547">[Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="af153-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="af153-548">Provedores de log de terceiros</span><span class="sxs-lookup"><span data-stu-id="af153-548">Third-party logging providers</span></span>

<span data-ttu-id="af153-549">Estruturas de log de terceiros que funcionam com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af153-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="af153-550">[elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="af153-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="af153-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="af153-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="af153-552">[JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="af153-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="af153-553">[KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="af153-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="af153-554">[Log4Net](https://logging.apache.org/log4net/) ([gitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="af153-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="af153-555">[Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="af153-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="af153-556">[NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="af153-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="af153-557">[Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="af153-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="af153-558">[Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="af153-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="af153-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="af153-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="af153-560">Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="af153-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="af153-561">Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:</span><span class="sxs-lookup"><span data-stu-id="af153-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="af153-562">Adicione um pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="af153-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="af153-563">Chame `ILoggerFactory` um método de extensão fornecido pela estrutura de registro.</span><span class="sxs-lookup"><span data-stu-id="af153-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="af153-564">Para saber mais, consulte a documentação de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="af153-565">Não há suporte para provedores de log de terceiros na Microsoft.</span><span class="sxs-lookup"><span data-stu-id="af153-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af153-566">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="af153-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af153-567">Por [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="af153-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="af153-568">O .NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="af153-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="af153-569">Este artigo mostra como usar a API de registro em log com provedores internos.</span><span class="sxs-lookup"><span data-stu-id="af153-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="af153-570">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af153-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="af153-571">Adicionar provedores</span><span class="sxs-lookup"><span data-stu-id="af153-571">Add providers</span></span>

<span data-ttu-id="af153-572">Um provedor de log exibe ou armazena logs.</span><span class="sxs-lookup"><span data-stu-id="af153-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="af153-573">Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="af153-574">Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="af153-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="af153-575">Para adicionar um provedor, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="af153-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="af153-576">O código anterior requer referências a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="af153-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="af153-577">O modelo de projeto padrão chama o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="af153-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="af153-578">Console</span><span class="sxs-lookup"><span data-stu-id="af153-578">Console</span></span>
* <span data-ttu-id="af153-579">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-579">Debug</span></span>
* <span data-ttu-id="af153-580">EventSource (a partir do ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="af153-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="af153-581">Se você usar `CreateDefaultBuilder`, poderá substituir os provedores padrão por aqueles que preferir.</span><span class="sxs-lookup"><span data-stu-id="af153-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="af153-582">Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.</span><span class="sxs-lookup"><span data-stu-id="af153-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="af153-583">Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.</span><span class="sxs-lookup"><span data-stu-id="af153-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="af153-584">Criar logs</span><span class="sxs-lookup"><span data-stu-id="af153-584">Create logs</span></span>

<span data-ttu-id="af153-585">Para criar logs, use um objeto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="af153-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="af153-586">Em um aplicativo Web ou serviço hospedado, obtenha um `ILogger` da DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="af153-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="af153-587">Em aplicativos de console não host, use o `LoggerFactory` para criar um `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="af153-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="af153-588">O exemplo do ASP.NET Core a seguir cria um agente de categoria `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="af153-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="af153-589">A *categoria* do log é uma cadeia de caracteres associada a cada log.</span><span class="sxs-lookup"><span data-stu-id="af153-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="af153-590">A instância de `ILogger<T>` cria logs que têm como a categoria o nome totalmente qualificado do tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="af153-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="af153-591">Nos exemplos de ASP.NET Core e aplicativo de console a seguir, o agente é usado para criar logs de nível `Information`.</span><span class="sxs-lookup"><span data-stu-id="af153-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="af153-592">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="af153-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="af153-593">[Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="af153-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="af153-594">Criar logs na inicialização</span><span class="sxs-lookup"><span data-stu-id="af153-594">Create logs in Startup</span></span>

<span data-ttu-id="af153-595">Para gravar logs na classe `Startup`, inclua um parâmetro `ILogger` na assinatura de construtor:</span><span class="sxs-lookup"><span data-stu-id="af153-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="af153-596">Criar logs na classe Programa</span><span class="sxs-lookup"><span data-stu-id="af153-596">Create logs in the Program class</span></span>

<span data-ttu-id="af153-597">Para gravar logs na classe `Program`, obtenha uma instância `ILogger` da DI:</span><span class="sxs-lookup"><span data-stu-id="af153-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="af153-598">O registro durante a construção do host não é suportado diretamente.</span><span class="sxs-lookup"><span data-stu-id="af153-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="af153-599">No entanto, um madeireiro separado pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="af153-599">However, a separate logger can be used.</span></span> <span data-ttu-id="af153-600">No exemplo a seguir, um [logger Serilog](https://serilog.net/) é usado para fazer login `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="af153-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="af153-601">`AddSerilog`usa a configuração `Log.Logger`estática especificada em:</span><span class="sxs-lookup"><span data-stu-id="af153-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="af153-602">Sem métodos de agente assíncronos</span><span class="sxs-lookup"><span data-stu-id="af153-602">No asynchronous logger methods</span></span>

<span data-ttu-id="af153-603">O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="af153-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="af153-604">Se o armazenamento de dados em log estiver lento, não grave diretamente nele.</span><span class="sxs-lookup"><span data-stu-id="af153-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="af153-605">Grave as mensagens de log em um repositório rápido primeiro e, depois, mova-as para um repositório lento.</span><span class="sxs-lookup"><span data-stu-id="af153-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="af153-606">Por exemplo, se você estiver enviado logs para o SQL Server, convém não fazer isso diretamente em um método `Log`, uma vez que os métodos `Log` são síncronos.</span><span class="sxs-lookup"><span data-stu-id="af153-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="af153-607">Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="af153-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="af153-608">Para obter mais informações, consulte este problema [do](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub.</span><span class="sxs-lookup"><span data-stu-id="af153-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="af153-609">Configuração</span><span class="sxs-lookup"><span data-stu-id="af153-609">Configuration</span></span>

<span data-ttu-id="af153-610">A configuração do provedor de logs é fornecida por um ou mais provedores de sincronização:</span><span class="sxs-lookup"><span data-stu-id="af153-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="af153-611">Formatos de arquivo (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="af153-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="af153-612">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="af153-612">Command-line arguments.</span></span>
* <span data-ttu-id="af153-613">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="af153-613">Environment variables.</span></span>
* <span data-ttu-id="af153-614">Objetos do .NET na memória.</span><span class="sxs-lookup"><span data-stu-id="af153-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="af153-615">O armazenamento do [Secret Manager](xref:security/app-secrets) não criptografado.</span><span class="sxs-lookup"><span data-stu-id="af153-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="af153-616">Um repositório de usuário criptografado, como o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="af153-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="af153-617">Provedores personalizados (instalados ou criados).</span><span class="sxs-lookup"><span data-stu-id="af153-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="af153-618">Por exemplo, a configuração de log geralmente é fornecida pela seção `Logging` dos arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="af153-619">O exemplo a seguir mostra o conteúdo de um típico arquivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="af153-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="af153-620">A propriedade `Logging` pode ter `LogLevel` e propriedades do provedor de logs (o Console é mostrado).</span><span class="sxs-lookup"><span data-stu-id="af153-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="af153-621">A propriedade `LogLevel` em `Logging` especifica o [nível](#log-level) mínimo para log nas categorias selecionadas.</span><span class="sxs-lookup"><span data-stu-id="af153-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="af153-622">No exemplo, as categorias `System` e `Microsoft` têm log no nível `Information`, e todas as outras no nível `Debug`.</span><span class="sxs-lookup"><span data-stu-id="af153-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="af153-623">Outras propriedades em `Logging` especificam provedores de logs.</span><span class="sxs-lookup"><span data-stu-id="af153-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="af153-624">O exemplo se refere ao provedor de Console.</span><span class="sxs-lookup"><span data-stu-id="af153-624">The example is for the Console provider.</span></span> <span data-ttu-id="af153-625">Se um provedor suportar [escopos de log,](#log-scopes) `IncludeScopes` indicará se eles estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="af153-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="af153-626">Uma propriedade de provedor (como `Console`, no exemplo) também pode especificar uma propriedade `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="af153-627">`LogLevel` em um provedor especifica os níveis de log para esse provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="af153-628">Se os níveis forem especificados em `Logging.{providername}.LogLevel`, eles substituirão o que estiver definido em `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="af153-629">A API de registro não inclui um cenário para alterar os níveis de log enquanto um aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="af153-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="af153-630">No entanto, alguns provedores de configuração são capazes de recarregar a configuração, o que tem efeito imediato na configuração de registro.</span><span class="sxs-lookup"><span data-stu-id="af153-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="af153-631">Por exemplo, o [Provedor de Configuração de Arquivos](xref:fundamentals/configuration/index#file-configuration-provider), que é adicionado `CreateDefaultBuilder` para ler arquivos de configurações, recarrega a configuração de registro por padrão.</span><span class="sxs-lookup"><span data-stu-id="af153-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="af153-632">Se a configuração for alterada em código enquanto um aplicativo estiver em execução, o aplicativo poderá chamar [iConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para atualizar a configuração de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="af153-633">Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="af153-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="af153-634">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="af153-634">Sample logging output</span></span>

<span data-ttu-id="af153-635">Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando.</span><span class="sxs-lookup"><span data-stu-id="af153-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="af153-636">Aqui está um exemplo da saída do console:</span><span class="sxs-lookup"><span data-stu-id="af153-636">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="af153-637">Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="af153-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="af153-638">Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af153-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="af153-639">Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="af153-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="af153-640">Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="af153-641">O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.</span><span class="sxs-lookup"><span data-stu-id="af153-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="af153-642">O restante deste artigo explica alguns detalhes e opções para registro em log.</span><span class="sxs-lookup"><span data-stu-id="af153-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="af153-643">Pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="af153-643">NuGet packages</span></span>

<span data-ttu-id="af153-644">As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="af153-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="af153-645">Categoria do log</span><span class="sxs-lookup"><span data-stu-id="af153-645">Log category</span></span>

<span data-ttu-id="af153-646">Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele.</span><span class="sxs-lookup"><span data-stu-id="af153-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="af153-647">Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="af153-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="af153-648">A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="af153-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="af153-649">Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:</span><span class="sxs-lookup"><span data-stu-id="af153-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="af153-650">Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="af153-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="af153-651">`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.</span><span class="sxs-lookup"><span data-stu-id="af153-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="af153-652">Nível de log</span><span class="sxs-lookup"><span data-stu-id="af153-652">Log level</span></span>

<span data-ttu-id="af153-653">Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="af153-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="af153-654">O nível de log indica a gravidade ou importância.</span><span class="sxs-lookup"><span data-stu-id="af153-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="af153-655">Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="af153-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="af153-656">O código a seguir cria os logs `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="af153-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="af153-657">No código anterior, o primeiro parâmetro é a [ID de evento de log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="af153-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="af153-658">O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes.</span><span class="sxs-lookup"><span data-stu-id="af153-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="af153-659">Os parâmetros de método serão explicados com posteriormente neste artigo, na [seção de modelos de mensagem](#log-message-template).</span><span class="sxs-lookup"><span data-stu-id="af153-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="af153-660">Os métodos de log que incluem o nível no nome do método (por exemplo, `LogInformation` e `LogWarning`) são [métodos de extensão para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="af153-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="af153-661">Esses métodos chamam um método `Log` que recebe um parâmetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="af153-662">Você pode chamar o método `Log` diretamente em vez de um desses métodos de extensão, mas a sintaxe é relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="af153-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="af153-663">Para saber mais, veja <xref:Microsoft.Extensions.Logging.ILogger> e o [código-fonte de extensões de agente](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="af153-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="af153-664">O ASP.NET Core define os seguintes níveis de log, ordenados aqui da menor para a maior gravidade.</span><span class="sxs-lookup"><span data-stu-id="af153-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="af153-665">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="af153-665">Trace = 0</span></span>

  <span data-ttu-id="af153-666">Para obter informações que normalmente são valiosas somente para depuração.</span><span class="sxs-lookup"><span data-stu-id="af153-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="af153-667">Essas mensagens podem conter dados confidenciais de aplicativos e, portanto, não devem ser habilitadas em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="af153-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="af153-668">*Desabilitado por padrão.*</span><span class="sxs-lookup"><span data-stu-id="af153-668">*Disabled by default.*</span></span>

* <span data-ttu-id="af153-669">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="af153-669">Debug = 1</span></span>

  <span data-ttu-id="af153-670">Para obter informações que possam ser úteis durante o desenvolvimento e a depuração.</span><span class="sxs-lookup"><span data-stu-id="af153-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="af153-671">Exemplo: `Entering method Configure with flag set to true.` habilite logs de nível `Debug` em produção somente ao solucionar problemas, devido ao alto volume de logs.</span><span class="sxs-lookup"><span data-stu-id="af153-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="af153-672">Information = 2</span><span class="sxs-lookup"><span data-stu-id="af153-672">Information = 2</span></span>

  <span data-ttu-id="af153-673">Para rastrear o fluxo geral do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="af153-674">Esses logs normalmente têm algum valor a longo prazo.</span><span class="sxs-lookup"><span data-stu-id="af153-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="af153-675">Exemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="af153-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="af153-676">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="af153-676">Warning = 3</span></span>

  <span data-ttu-id="af153-677">Para eventos anormais ou inesperados no fluxo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="af153-678">Eles podem incluir erros ou outras condições que não fazem com que o aplicativo pare, mas que talvez precisem ser investigados.</span><span class="sxs-lookup"><span data-stu-id="af153-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="af153-679">Exceções manipuladas são um local comum para usar o nível de log `Warning`.</span><span class="sxs-lookup"><span data-stu-id="af153-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="af153-680">Exemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="af153-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="af153-681">Error = 4</span><span class="sxs-lookup"><span data-stu-id="af153-681">Error = 4</span></span>

  <span data-ttu-id="af153-682">Para erros e exceções que não podem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="af153-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="af153-683">Essas mensagens indicam uma falha na atividade ou na operação atual (como a solicitação HTTP atual) e não uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="af153-684">Mensagem de log de exemplo:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="af153-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="af153-685">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="af153-685">Critical = 5</span></span>

  <span data-ttu-id="af153-686">Para falhas que exigem atenção imediata.</span><span class="sxs-lookup"><span data-stu-id="af153-686">For failures that require immediate attention.</span></span> <span data-ttu-id="af153-687">Exemplos: cenários de perda de dados, espaço em disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="af153-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="af153-688">Use o nível de log para controlar a quantidade de saída de log que é gravada em uma mídia de armazenamento específica ou em uma janela de exibição.</span><span class="sxs-lookup"><span data-stu-id="af153-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="af153-689">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af153-689">For example:</span></span>

* <span data-ttu-id="af153-690">Em produção:</span><span class="sxs-lookup"><span data-stu-id="af153-690">In production:</span></span>
  * <span data-ttu-id="af153-691">O registro `Trace` `Information` nos níveis de passagem produz um alto volume de mensagens de log detalhadas.</span><span class="sxs-lookup"><span data-stu-id="af153-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="af153-692">Para controlar custos e não exceder `Trace` `Information` os limites de armazenamento de dados, faça login através de mensagens de nível para um armazenamento de dados de alto volume e baixo custo.</span><span class="sxs-lookup"><span data-stu-id="af153-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="af153-693">O `Warning` registro `Critical` em níveis tipicamente produz menos mensagens de registro menores.</span><span class="sxs-lookup"><span data-stu-id="af153-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="af153-694">Portanto, custos e limites de armazenamento geralmente não são uma preocupação, o que resulta em maior flexibilidade na escolha do armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="af153-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="af153-695">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="af153-695">During development:</span></span>
  * <span data-ttu-id="af153-696">Faça `Warning` `Critical` login através de mensagens para o console.</span><span class="sxs-lookup"><span data-stu-id="af153-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="af153-697">Adicione `Trace` `Information` mensagens ao solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="af153-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="af153-698">A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.</span><span class="sxs-lookup"><span data-stu-id="af153-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="af153-699">O ASP.NET Core grava logs para eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="af153-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="af153-700">Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados.</span><span class="sxs-lookup"><span data-stu-id="af153-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="af153-701">Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:</span><span class="sxs-lookup"><span data-stu-id="af153-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="af153-702">ID de evento de log</span><span class="sxs-lookup"><span data-stu-id="af153-702">Log event ID</span></span>

<span data-ttu-id="af153-703">Cada log pode especificar uma *ID do evento*.</span><span class="sxs-lookup"><span data-stu-id="af153-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="af153-704">O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:</span><span class="sxs-lookup"><span data-stu-id="af153-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="af153-705">Uma ID de evento associa um conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="af153-705">An event ID associates a set of events.</span></span> <span data-ttu-id="af153-706">Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.</span><span class="sxs-lookup"><span data-stu-id="af153-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="af153-707">O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar.</span><span class="sxs-lookup"><span data-stu-id="af153-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="af153-708">O provedor de Depuração não mostra IDs de eventos.</span><span class="sxs-lookup"><span data-stu-id="af153-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="af153-709">O provedor de console mostra IDs de evento entre colchetes após a categoria:</span><span class="sxs-lookup"><span data-stu-id="af153-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="af153-710">Modelo de mensagem de log</span><span class="sxs-lookup"><span data-stu-id="af153-710">Log message template</span></span>

<span data-ttu-id="af153-711">Cada log especifica um modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="af153-711">Each log specifies a message template.</span></span> <span data-ttu-id="af153-712">O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos.</span><span class="sxs-lookup"><span data-stu-id="af153-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="af153-713">Use nomes para os espaços reservados, não números.</span><span class="sxs-lookup"><span data-stu-id="af153-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="af153-714">A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores.</span><span class="sxs-lookup"><span data-stu-id="af153-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="af153-715">No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:</span><span class="sxs-lookup"><span data-stu-id="af153-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="af153-716">Esse código cria uma mensagem de log com os valores de parâmetro na sequência:</span><span class="sxs-lookup"><span data-stu-id="af153-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="af153-717">A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="af153-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="af153-718">Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado.</span><span class="sxs-lookup"><span data-stu-id="af153-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="af153-719">Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos.</span><span class="sxs-lookup"><span data-stu-id="af153-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="af153-720">Por exemplo, suponha que as chamadas de método do agente sejam assim:</span><span class="sxs-lookup"><span data-stu-id="af153-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="af153-721">Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log.</span><span class="sxs-lookup"><span data-stu-id="af153-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="af153-722">Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.</span><span class="sxs-lookup"><span data-stu-id="af153-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="af153-723">Exceções de registro em log</span><span class="sxs-lookup"><span data-stu-id="af153-723">Logging exceptions</span></span>

<span data-ttu-id="af153-724">Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="af153-725">Provedores diferentes manipulam as informações de exceção de maneiras diferentes.</span><span class="sxs-lookup"><span data-stu-id="af153-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="af153-726">Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="af153-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="af153-727">Filtragem de linha</span><span class="sxs-lookup"><span data-stu-id="af153-727">Log filtering</span></span>

<span data-ttu-id="af153-728">Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias.</span><span class="sxs-lookup"><span data-stu-id="af153-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="af153-729">Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.</span><span class="sxs-lookup"><span data-stu-id="af153-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="af153-730">Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="af153-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="af153-731">O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="af153-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="af153-732">Criar regras de filtro na configuração</span><span class="sxs-lookup"><span data-stu-id="af153-732">Create filter rules in configuration</span></span>

<span data-ttu-id="af153-733">O código de `CreateDefaultBuilder` modelo de projeto chama para configurar o registro para os provedores Console, Debug e EventSource (ASP.NET Core 2.2 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="af153-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="af153-734">O método `CreateDefaultBuilder` configura o registro em log para procurar a configuração em uma seção `Logging`, conforme explicado [anteriormente neste artigo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="af153-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="af153-735">Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="af153-736">Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores.</span><span class="sxs-lookup"><span data-stu-id="af153-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="af153-737">Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.</span><span class="sxs-lookup"><span data-stu-id="af153-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="af153-738">Regras de filtro no código</span><span class="sxs-lookup"><span data-stu-id="af153-738">Filter rules in code</span></span>

<span data-ttu-id="af153-739">O exemplo a seguir mostra como registrar regras de filtro no código:</span><span class="sxs-lookup"><span data-stu-id="af153-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="af153-740">O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo.</span><span class="sxs-lookup"><span data-stu-id="af153-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="af153-741">O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="af153-742">Como as regras de filtragem são aplicadas</span><span class="sxs-lookup"><span data-stu-id="af153-742">How filtering rules are applied</span></span>

<span data-ttu-id="af153-743">Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="af153-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="af153-744">As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.</span><span class="sxs-lookup"><span data-stu-id="af153-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="af153-745">Número</span><span class="sxs-lookup"><span data-stu-id="af153-745">Number</span></span> | <span data-ttu-id="af153-746">Provedor</span><span class="sxs-lookup"><span data-stu-id="af153-746">Provider</span></span>      | <span data-ttu-id="af153-747">Categorias que começam com...</span><span class="sxs-lookup"><span data-stu-id="af153-747">Categories that begin with ...</span></span>          | <span data-ttu-id="af153-748">Nível de log mínimo</span><span class="sxs-lookup"><span data-stu-id="af153-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="af153-749">1</span><span class="sxs-lookup"><span data-stu-id="af153-749">1</span></span>      | <span data-ttu-id="af153-750">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-750">Debug</span></span>         | <span data-ttu-id="af153-751">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="af153-751">All categories</span></span>                          | <span data-ttu-id="af153-752">Informações</span><span class="sxs-lookup"><span data-stu-id="af153-752">Information</span></span>       |
| <span data-ttu-id="af153-753">2</span><span class="sxs-lookup"><span data-stu-id="af153-753">2</span></span>      | <span data-ttu-id="af153-754">Console</span><span class="sxs-lookup"><span data-stu-id="af153-754">Console</span></span>       | <span data-ttu-id="af153-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="af153-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="af153-756">Aviso</span><span class="sxs-lookup"><span data-stu-id="af153-756">Warning</span></span>           |
| <span data-ttu-id="af153-757">3</span><span class="sxs-lookup"><span data-stu-id="af153-757">3</span></span>      | <span data-ttu-id="af153-758">Console</span><span class="sxs-lookup"><span data-stu-id="af153-758">Console</span></span>       | <span data-ttu-id="af153-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="af153-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="af153-760">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-760">Debug</span></span>             |
| <span data-ttu-id="af153-761">4</span><span class="sxs-lookup"><span data-stu-id="af153-761">4</span></span>      | <span data-ttu-id="af153-762">Console</span><span class="sxs-lookup"><span data-stu-id="af153-762">Console</span></span>       | <span data-ttu-id="af153-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="af153-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="af153-764">Erro</span><span class="sxs-lookup"><span data-stu-id="af153-764">Error</span></span>             |
| <span data-ttu-id="af153-765">5</span><span class="sxs-lookup"><span data-stu-id="af153-765">5</span></span>      | <span data-ttu-id="af153-766">Console</span><span class="sxs-lookup"><span data-stu-id="af153-766">Console</span></span>       | <span data-ttu-id="af153-767">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="af153-767">All categories</span></span>                          | <span data-ttu-id="af153-768">Informações</span><span class="sxs-lookup"><span data-stu-id="af153-768">Information</span></span>       |
| <span data-ttu-id="af153-769">6</span><span class="sxs-lookup"><span data-stu-id="af153-769">6</span></span>      | <span data-ttu-id="af153-770">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="af153-770">All providers</span></span> | <span data-ttu-id="af153-771">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="af153-771">All categories</span></span>                          | <span data-ttu-id="af153-772">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-772">Debug</span></span>             |
| <span data-ttu-id="af153-773">7</span><span class="sxs-lookup"><span data-stu-id="af153-773">7</span></span>      | <span data-ttu-id="af153-774">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="af153-774">All providers</span></span> | <span data-ttu-id="af153-775">Sistema</span><span class="sxs-lookup"><span data-stu-id="af153-775">System</span></span>                                  | <span data-ttu-id="af153-776">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-776">Debug</span></span>             |
| <span data-ttu-id="af153-777">8</span><span class="sxs-lookup"><span data-stu-id="af153-777">8</span></span>      | <span data-ttu-id="af153-778">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-778">Debug</span></span>         | <span data-ttu-id="af153-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="af153-779">Microsoft</span></span>                               | <span data-ttu-id="af153-780">Trace</span><span class="sxs-lookup"><span data-stu-id="af153-780">Trace</span></span>             |

<span data-ttu-id="af153-781">Quando um objeto `ILogger` é criado, o objeto `ILoggerFactory` seleciona uma única regra por provedor para aplicar a esse agente.</span><span class="sxs-lookup"><span data-stu-id="af153-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="af153-782">Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas.</span><span class="sxs-lookup"><span data-stu-id="af153-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="af153-783">A regra mais específica possível para cada par de categoria e provedor é selecionada dentre as regras disponíveis.</span><span class="sxs-lookup"><span data-stu-id="af153-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="af153-784">O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:</span><span class="sxs-lookup"><span data-stu-id="af153-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="af153-785">Selecione todas as regras que correspondem ao provedor ou seu alias.</span><span class="sxs-lookup"><span data-stu-id="af153-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="af153-786">Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.</span><span class="sxs-lookup"><span data-stu-id="af153-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="af153-787">Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência.</span><span class="sxs-lookup"><span data-stu-id="af153-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="af153-788">Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.</span><span class="sxs-lookup"><span data-stu-id="af153-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="af153-789">Se várias regras forem selecionadas, use a **última**.</span><span class="sxs-lookup"><span data-stu-id="af153-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="af153-790">Se nenhuma regra for selecionada, use `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="af153-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="af153-791">Com a lista anterior de regras, suponha que você crie um objeto `ILogger` para a categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="af153-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="af153-792">Para o provedor Depuração as regras 1, 6 e 8 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="af153-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="af153-793">A regra 8 é mais específica, portanto é a que será selecionada.</span><span class="sxs-lookup"><span data-stu-id="af153-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="af153-794">Para o provedor Console as regras 3, 4, 5 e 6 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="af153-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="af153-795">A regra 3 é a mais específica.</span><span class="sxs-lookup"><span data-stu-id="af153-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="af153-796">A instância `ILogger` resultante envia logs de nível `Trace` e superior para o provedor Depuração.</span><span class="sxs-lookup"><span data-stu-id="af153-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="af153-797">Logs de nível `Debug` e superior são enviados para o provedor Console.</span><span class="sxs-lookup"><span data-stu-id="af153-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="af153-798">Aliases de provedor</span><span class="sxs-lookup"><span data-stu-id="af153-798">Provider aliases</span></span>

<span data-ttu-id="af153-799">Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="af153-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="af153-800">Para os provedores internos, use os seguintes aliases:</span><span class="sxs-lookup"><span data-stu-id="af153-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="af153-801">Console</span><span class="sxs-lookup"><span data-stu-id="af153-801">Console</span></span>
* <span data-ttu-id="af153-802">Depurar</span><span class="sxs-lookup"><span data-stu-id="af153-802">Debug</span></span>
* <span data-ttu-id="af153-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="af153-803">EventSource</span></span>
* <span data-ttu-id="af153-804">EventLog</span><span class="sxs-lookup"><span data-stu-id="af153-804">EventLog</span></span>
* <span data-ttu-id="af153-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="af153-805">TraceSource</span></span>
* <span data-ttu-id="af153-806">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="af153-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="af153-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="af153-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="af153-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="af153-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="af153-809">Nível mínimo padrão</span><span class="sxs-lookup"><span data-stu-id="af153-809">Default minimum level</span></span>

<span data-ttu-id="af153-810">Há uma configuração de nível mínimo que entra em vigor somente se nenhuma regra de código ou de configuração se aplicar a um provedor e uma categoria determinados.</span><span class="sxs-lookup"><span data-stu-id="af153-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="af153-811">O exemplo a seguir mostra como definir o nível mínimo:</span><span class="sxs-lookup"><span data-stu-id="af153-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="af153-812">Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="af153-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="af153-813">Funções de filtro</span><span class="sxs-lookup"><span data-stu-id="af153-813">Filter functions</span></span>

<span data-ttu-id="af153-814">Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código.</span><span class="sxs-lookup"><span data-stu-id="af153-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="af153-815">O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log.</span><span class="sxs-lookup"><span data-stu-id="af153-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="af153-816">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af153-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="af153-817">Categorias e níveis de sistema</span><span class="sxs-lookup"><span data-stu-id="af153-817">System categories and levels</span></span>

<span data-ttu-id="af153-818">Veja algumas categorias usadas pelo ASP.NET Core e Entity Framework Core, com anotações sobre quais logs esperar delas:</span><span class="sxs-lookup"><span data-stu-id="af153-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="af153-819">Categoria</span><span class="sxs-lookup"><span data-stu-id="af153-819">Category</span></span>                            | <span data-ttu-id="af153-820">Observações</span><span class="sxs-lookup"><span data-stu-id="af153-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="af153-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="af153-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="af153-822">Diagnóstico geral de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="af153-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="af153-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="af153-824">Quais chaves foram consideradas, encontradas e usadas.</span><span class="sxs-lookup"><span data-stu-id="af153-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="af153-825">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="af153-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="af153-826">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="af153-826">Hosts allowed.</span></span> |
| <span data-ttu-id="af153-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="af153-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="af153-828">Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas.</span><span class="sxs-lookup"><span data-stu-id="af153-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="af153-829">Quais assemblies de inicialização de hospedagem foram carregados.</span><span class="sxs-lookup"><span data-stu-id="af153-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="af153-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="af153-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="af153-831">Diagnóstico do MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="af153-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="af153-832">Model binding, execução de filtro, compilação de exibição, seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="af153-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="af153-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="af153-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="af153-834">Informações de correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="af153-834">Route matching information.</span></span> |
| <span data-ttu-id="af153-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="af153-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="af153-836">Respostas de início, parada e atividade da conexão.</span><span class="sxs-lookup"><span data-stu-id="af153-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="af153-837">Informações sobre o certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af153-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="af153-838">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="af153-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="af153-839">Arquivos atendidos.</span><span class="sxs-lookup"><span data-stu-id="af153-839">Files served.</span></span> |
| <span data-ttu-id="af153-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="af153-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="af153-841">Diagnóstico geral do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="af153-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="af153-842">Atividade e configuração do banco de dados, detecção de alterações, migrações.</span><span class="sxs-lookup"><span data-stu-id="af153-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="af153-843">Escopos de log</span><span class="sxs-lookup"><span data-stu-id="af153-843">Log scopes</span></span>

 <span data-ttu-id="af153-844">Um *escopo* pode agrupar um conjunto de operações lógicas.</span><span class="sxs-lookup"><span data-stu-id="af153-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="af153-845">Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto.</span><span class="sxs-lookup"><span data-stu-id="af153-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="af153-846">Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.</span><span class="sxs-lookup"><span data-stu-id="af153-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="af153-847">Um escopo é um tipo `IDisposable` retornado pelo método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e que dura até que seja descartado.</span><span class="sxs-lookup"><span data-stu-id="af153-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="af153-848">Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:</span><span class="sxs-lookup"><span data-stu-id="af153-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="af153-849">O código a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="af153-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="af153-850">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="af153-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="af153-851">A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.</span><span class="sxs-lookup"><span data-stu-id="af153-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="af153-852">Saiba mais sobre como configurar na seção [Configuração](#configuration).</span><span class="sxs-lookup"><span data-stu-id="af153-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="af153-853">Cada mensagem de log inclui as informações com escopo definido:</span><span class="sxs-lookup"><span data-stu-id="af153-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="af153-854">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="af153-854">Built-in logging providers</span></span>

<span data-ttu-id="af153-855">O ASP.NET Core vem com os seguintes provedores:</span><span class="sxs-lookup"><span data-stu-id="af153-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="af153-856">Console</span><span class="sxs-lookup"><span data-stu-id="af153-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="af153-857">Depuração</span><span class="sxs-lookup"><span data-stu-id="af153-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="af153-858">Eventsource</span><span class="sxs-lookup"><span data-stu-id="af153-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="af153-859">EventLog</span><span class="sxs-lookup"><span data-stu-id="af153-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="af153-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="af153-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="af153-861">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="af153-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="af153-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="af153-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="af153-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="af153-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="af153-864">Para obter informações sobre StdOut e o log de depuração com o Módulo do ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="af153-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="af153-865">Provedor do console</span><span class="sxs-lookup"><span data-stu-id="af153-865">Console provider</span></span>

<span data-ttu-id="af153-866">O pacote de provedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envia a saída de log para o console.</span><span class="sxs-lookup"><span data-stu-id="af153-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="af153-867">Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="af153-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="af153-868">Depurar provedor</span><span class="sxs-lookup"><span data-stu-id="af153-868">Debug provider</span></span>

<span data-ttu-id="af153-869">O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="af153-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="af153-870">No Linux, esse provedor grava logs em */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="af153-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="af153-871">Provedor de Origem de Eventos</span><span class="sxs-lookup"><span data-stu-id="af153-871">Event Source provider</span></span>

<span data-ttu-id="af153-872">O pacote [microsoft.extensions.logging.eventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) é escrito em uma plataforma `Microsoft-Extensions-Logging`multiplataforma de origem de evento com o nome .</span><span class="sxs-lookup"><span data-stu-id="af153-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="af153-873">No Windows, o provedor usa [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="af153-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="af153-874">O provedor de origem de `CreateDefaultBuilder` eventos é adicionado automaticamente quando é chamado para construir o host.</span><span class="sxs-lookup"><span data-stu-id="af153-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="af153-875">Use o [utilitário PerfView](https://github.com/Microsoft/perfview) para coletar e exibir logs.</span><span class="sxs-lookup"><span data-stu-id="af153-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="af153-876">Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="af153-877">Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**.</span><span class="sxs-lookup"><span data-stu-id="af153-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="af153-878">(Não se esqueça do asterisco no início da cadeia de caracteres).</span><span class="sxs-lookup"><span data-stu-id="af153-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="af153-880">Provedor EventLog do Windows</span><span class="sxs-lookup"><span data-stu-id="af153-880">Windows EventLog provider</span></span>

<span data-ttu-id="af153-881">O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="af153-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="af153-882">As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="af153-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="af153-883">Se `null` especificado ou não, as seguintes configurações padrão serão usadas:</span><span class="sxs-lookup"><span data-stu-id="af153-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="af153-884">`LogName`&ndash; "Aplicação"</span><span class="sxs-lookup"><span data-stu-id="af153-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="af153-885">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="af153-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="af153-886">`MachineName` &ndash; computador local</span><span class="sxs-lookup"><span data-stu-id="af153-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="af153-887">Os eventos são registrados para [nível de aviso e superior .](#log-level)</span><span class="sxs-lookup"><span data-stu-id="af153-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="af153-888">Para registrar eventos `Warning`inferiores, defina explicitamente o nível de registro.</span><span class="sxs-lookup"><span data-stu-id="af153-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="af153-889">Por exemplo, adicione o seguinte ao arquivo *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="af153-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="af153-890">Provedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="af153-890">TraceSource provider</span></span>

<span data-ttu-id="af153-891">O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="af153-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="af153-892">As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="af153-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="af153-893">Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="af153-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="af153-894">O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="af153-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="af153-895">Provedor do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="af153-895">Azure App Service provider</span></span>

<span data-ttu-id="af153-896">O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="af153-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="af153-897">O pacote de provedor não está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="af153-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="af153-898">Se você estiver direcionando para o .NET Framework ou referenciando o metapacote `Microsoft.AspNetCore.App`, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="af153-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="af153-899">Uma sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite passar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="af153-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="af153-900">O objeto settings pode substituir as configurações padrão, como o modelo de saída de registro em log, o nome do blob e o limite do tamanho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="af153-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="af153-901">(O *modelo Output* é um modelo de mensagem aplicado a todos os logs, além daquele fornecido com uma chamada ao método `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="af153-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="af153-902">Quando você implanta em um aplicativo do Serviço de Aplicativo, o aplicativo respeita as configurações na seção [Logs do Serviço de Aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) da página **Serviço de Aplicativo** do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="af153-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="af153-903">Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="af153-904">**Log de aplicativo (Sistema de Arquivos)**</span><span class="sxs-lookup"><span data-stu-id="af153-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="af153-905">**Log de aplicativo (Blob)**</span><span class="sxs-lookup"><span data-stu-id="af153-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="af153-906">O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="af153-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="af153-907">O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2.</span><span class="sxs-lookup"><span data-stu-id="af153-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="af153-908">O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="af153-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="af153-909">O provedor funciona somente quando o projeto é executado no ambiente do Azure.</span><span class="sxs-lookup"><span data-stu-id="af153-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="af153-910">Ele não tem nenhum efeito quando o projeto é executado localmente&mdash;ele não grava em arquivos locais ou no armazenamento de desenvolvimento local para blobs.</span><span class="sxs-lookup"><span data-stu-id="af153-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="af153-911">Fluxo de log do Azure</span><span class="sxs-lookup"><span data-stu-id="af153-911">Azure log streaming</span></span>

<span data-ttu-id="af153-912">O fluxo de log do Azure permite que você exiba a atividade de log em tempo real:</span><span class="sxs-lookup"><span data-stu-id="af153-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="af153-913">O servidor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="af153-913">The app server</span></span>
* <span data-ttu-id="af153-914">Do servidor Web</span><span class="sxs-lookup"><span data-stu-id="af153-914">The web server</span></span>
* <span data-ttu-id="af153-915">De uma solicitação de rastreio com falha</span><span class="sxs-lookup"><span data-stu-id="af153-915">Failed request tracing</span></span>

<span data-ttu-id="af153-916">Para configurar o fluxo de log do Azure:</span><span class="sxs-lookup"><span data-stu-id="af153-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="af153-917">Navegue até a página **Logs do Serviço de Aplicativo** da página do portal do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="af153-918">Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**.</span><span class="sxs-lookup"><span data-stu-id="af153-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="af153-919">Escolha o **Nível** de log.</span><span class="sxs-lookup"><span data-stu-id="af153-919">Choose the log **Level**.</span></span> <span data-ttu-id="af153-920">Essa configuração só se aplica ao streaming de log do Azure, não a outros provedores de registro no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="af153-921">Navegue até a página **Fluxo de Log** para exibir as mensagens de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="af153-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="af153-922">Elas são registradas pelo aplicativo por meio da interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="af153-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="af153-923">Log de rastreamento do Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="af153-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="af153-924">O pacote de provedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava os logs no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="af153-925">O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria.</span><span class="sxs-lookup"><span data-stu-id="af153-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="af153-926">Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="af153-927">O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af153-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="af153-928">Se você usar esse pacote, não precisará instalar o pacote de provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="af153-929">Não use o pacote [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que é para o ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="af153-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="af153-930">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="af153-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="af153-931">Visão geral do Application Insights</span><span class="sxs-lookup"><span data-stu-id="af153-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="af153-932">[Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.</span><span class="sxs-lookup"><span data-stu-id="af153-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="af153-933">[ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="af153-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="af153-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs) (Adaptadores de registro em log do Application Insights).</span><span class="sxs-lookup"><span data-stu-id="af153-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="af153-935">[Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="af153-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="af153-936">Provedores de log de terceiros</span><span class="sxs-lookup"><span data-stu-id="af153-936">Third-party logging providers</span></span>

<span data-ttu-id="af153-937">Estruturas de log de terceiros que funcionam com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af153-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="af153-938">[elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="af153-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="af153-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="af153-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="af153-940">[JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="af153-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="af153-941">[KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="af153-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="af153-942">[Log4Net](https://logging.apache.org/log4net/) ([gitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="af153-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="af153-943">[Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="af153-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="af153-944">[NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="af153-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="af153-945">[Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="af153-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="af153-946">[Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="af153-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="af153-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="af153-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="af153-948">Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="af153-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="af153-949">Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:</span><span class="sxs-lookup"><span data-stu-id="af153-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="af153-950">Adicione um pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="af153-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="af153-951">Chame `ILoggerFactory` um método de extensão fornecido pela estrutura de registro.</span><span class="sxs-lookup"><span data-stu-id="af153-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="af153-952">Para saber mais, consulte a documentação de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="af153-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="af153-953">Não há suporte para provedores de log de terceiros na Microsoft.</span><span class="sxs-lookup"><span data-stu-id="af153-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af153-954">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="af153-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
