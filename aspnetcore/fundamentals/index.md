---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: da2b42a7cf5d116a36d1dd9fa586d40ab31fc52d
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417651"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="b0a5f-103">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0a5f-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0a5f-104">Este artigo fornece uma visão geral dos principais tópicos para entender como desenvolver ASP.NET aplicativos Core.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="b0a5f-105">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="b0a5f-105">The Startup class</span></span>

<span data-ttu-id="b0a5f-106">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="b0a5f-107">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="b0a5f-108">O pipeline de tratamento de solicitação do aplicativo é definido como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="b0a5f-109">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="b0a5f-110">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="b0a5f-111">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-111">Dependency injection (services)</span></span>

<span data-ttu-id="b0a5f-112">ASP.NET Core inclui uma estrutura di (built-in dependency injection, injeção de dependência) incorporada que disponibiliza serviços configurados em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="b0a5f-113">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="b0a5f-114">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b0a5f-115">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="b0a5f-116">Os serviços são normalmente resolvidos a partir de DI usando injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="b0a5f-117">Com a injeção de construtor, uma classe declara um parâmetro construtor do tipo necessário ou de uma interface.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="b0a5f-118">O framework DI fornece uma instância deste serviço em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="b0a5f-119">O exemplo a seguir usa `RazorPagesMovieContext` injeção de construtor para resolver a de DI:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="b0a5f-120">Se o contêiner Inversão de Controle (IoC) embutido não atender a todas as necessidades de um aplicativo, um contêiner IoC de terceiros pode ser usado em vez disso.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="b0a5f-121">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="b0a5f-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="b0a5f-122">Middleware</span></span>

<span data-ttu-id="b0a5f-123">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="b0a5f-124">Cada componente executa `HttpContext` operações em um e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="b0a5f-125">Por convenção, um componente middleware é adicionado `Use...` ao pipeline `Startup.Configure` invocando um método de extensão no método.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="b0a5f-126">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="b0a5f-127">O exemplo a seguir configura um pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="b0a5f-128">ASP.NET Core inclui um rico conjunto de middleware embutido.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="b0a5f-129">Componentes de middleware personalizados também podem ser escritos.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="b0a5f-130">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="b0a5f-131">Host</span><span class="sxs-lookup"><span data-stu-id="b0a5f-131">Host</span></span>

<span data-ttu-id="b0a5f-132">Na inicialização, um aplicativo ASP.NET Core constrói um *host*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="b0a5f-133">O host encapsula todos os recursos do aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="b0a5f-134">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="b0a5f-134">An HTTP server implementation</span></span>
* <span data-ttu-id="b0a5f-135">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="b0a5f-135">Middleware components</span></span>
* <span data-ttu-id="b0a5f-136">Registro em log</span><span class="sxs-lookup"><span data-stu-id="b0a5f-136">Logging</span></span>
* <span data-ttu-id="b0a5f-137">Serviços de injeção de dependência (DI)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="b0a5f-138">Configuração</span><span class="sxs-lookup"><span data-stu-id="b0a5f-138">Configuration</span></span>

<span data-ttu-id="b0a5f-139">Existem dois hosts diferentes:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-139">There are two different hosts:</span></span> 

* <span data-ttu-id="b0a5f-140">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="b0a5f-140">.NET Generic Host</span></span>
* <span data-ttu-id="b0a5f-141">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0a5f-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="b0a5f-142">Recomenda-se o .NET Generic Host.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="b0a5f-143">O ASP.NET Core Web Host está disponível apenas para retrocompatibilidade.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="b0a5f-144">O exemplo a seguir cria um Host Genérico .NET:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="b0a5f-145">Os `CreateDefaultBuilder` `ConfigureWebHostDefaults` métodos configuram um host com um conjunto de opções padrão, tais como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="b0a5f-146">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="b0a5f-147">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="b0a5f-148">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="b0a5f-149">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="b0a5f-150">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="b0a5f-150">Non-web scenarios</span></span>

<span data-ttu-id="b0a5f-151">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="b0a5f-152">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="b0a5f-153">Servidores</span><span class="sxs-lookup"><span data-stu-id="b0a5f-153">Servers</span></span>

<span data-ttu-id="b0a5f-154">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="b0a5f-155">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="b0a5f-156">Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="b0a5f-157">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="b0a5f-158">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="b0a5f-159">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="b0a5f-160">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="b0a5f-161">*IIS HTTP Server* é um servidor para Windows que usa IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="b0a5f-162">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="b0a5f-163">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="b0a5f-164">macOS</span><span class="sxs-lookup"><span data-stu-id="b0a5f-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="b0a5f-165">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="b0a5f-166">Em ASP.NET Core 2.0 ou posterior, o Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="b0a5f-167">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="b0a5f-168">Linux</span><span class="sxs-lookup"><span data-stu-id="b0a5f-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="b0a5f-169">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="b0a5f-170">Em ASP.NET Core 2.0 ou posterior, o Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="b0a5f-171">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="b0a5f-172">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="b0a5f-173">Configuração</span><span class="sxs-lookup"><span data-stu-id="b0a5f-173">Configuration</span></span>

<span data-ttu-id="b0a5f-174">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="b0a5f-175">Os provedores de configuração incorporada estão disponíveis para uma variedade de fontes, como arquivos *.json,* arquivos *.xml,* variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="b0a5f-176">Escreva provedores de configuração personalizados para suportar outras fontes.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="b0a5f-177">Por [padrão,](xref:fundamentals/configuration/index#default)ASP.NET os aplicativos Core são configurados para ler a partir de *appsettings.json*, variáveis de ambiente, a linha de comando e muito mais.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="b0a5f-178">Quando a configuração do aplicativo é carregada, os valores das variáveis do ambiente sobrepõem os valores de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="b0a5f-179">A maneira preferida de ler valores de configuração relacionados é usando o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b0a5f-180">Para obter mais informações, consulte [Vincular dados de configuração hierárquica usando o padrão de opções](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="b0a5f-181">Para gerenciar dados confidenciais de configuração, como senhas, o ASP.NET Core fornece o [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="b0a5f-182">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="b0a5f-183">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="b0a5f-184">Ambientes</span><span class="sxs-lookup"><span data-stu-id="b0a5f-184">Environments</span></span>

<span data-ttu-id="b0a5f-185">Ambientes de execução, como `Development`, `Staging`e `Production`, são uma noção de primeira classe em ASP.NET Núcleo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="b0a5f-186">Especifique o ambiente em `ASPNETCORE_ENVIRONMENT` que um aplicativo está sendo executado definindo a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="b0a5f-187">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="b0a5f-188">Esta implementação está disponível em qualquer lugar em um aplicativo via injeção de dependência (DI).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="b0a5f-189">O exemplo a seguir configura o aplicativo para `Development` fornecer informações detalhadas de erro ao ser executado no ambiente:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="b0a5f-190">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="b0a5f-191">Registro em log</span><span class="sxs-lookup"><span data-stu-id="b0a5f-191">Logging</span></span>

<span data-ttu-id="b0a5f-192">O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b0a5f-193">Os provedores disponíveis incluem:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-193">Available providers include:</span></span>

* <span data-ttu-id="b0a5f-194">Console</span><span class="sxs-lookup"><span data-stu-id="b0a5f-194">Console</span></span>
* <span data-ttu-id="b0a5f-195">Depurar</span><span class="sxs-lookup"><span data-stu-id="b0a5f-195">Debug</span></span>
* <span data-ttu-id="b0a5f-196">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="b0a5f-197">Log de eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-197">Windows Event Log</span></span>
* <span data-ttu-id="b0a5f-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b0a5f-198">TraceSource</span></span>
* <span data-ttu-id="b0a5f-199">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="b0a5f-199">Azure App Service</span></span>
* <span data-ttu-id="b0a5f-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="b0a5f-200">Azure Application Insights</span></span>

<span data-ttu-id="b0a5f-201">Para criar logs, <xref:Microsoft.Extensions.Logging.ILogger%601> resolva um serviço a partir de injeção <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>de dependência (DI) e chame métodos de registro de chamadas como .</span><span class="sxs-lookup"><span data-stu-id="b0a5f-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="b0a5f-202">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="b0a5f-203">Métodos de `LogInformation` registro, como suporte a qualquer número de campos.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="b0a5f-204">Esses campos são comumente usados `string`para construir uma mensagem, mas alguns provedores de registro os enviam para um armazenamento de dados como campos separados.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="b0a5f-205">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b0a5f-206">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="b0a5f-207">Roteamento</span><span class="sxs-lookup"><span data-stu-id="b0a5f-207">Routing</span></span>

<span data-ttu-id="b0a5f-208">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="b0a5f-209">O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="b0a5f-210">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="b0a5f-211">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="b0a5f-212">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="b0a5f-212">Error handling</span></span>

<span data-ttu-id="b0a5f-213">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="b0a5f-214">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="b0a5f-214">A developer exception page</span></span>
* <span data-ttu-id="b0a5f-215">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="b0a5f-215">Custom error pages</span></span>
* <span data-ttu-id="b0a5f-216">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="b0a5f-216">Static status code pages</span></span>
* <span data-ttu-id="b0a5f-217">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="b0a5f-217">Startup exception handling</span></span>

<span data-ttu-id="b0a5f-218">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="b0a5f-219">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="b0a5f-219">Make HTTP requests</span></span>

<span data-ttu-id="b0a5f-220">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="b0a5f-221">O alocador:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-221">The factory:</span></span>

* <span data-ttu-id="b0a5f-222">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b0a5f-223">Por exemplo, registre-se e configure um cliente *github* para acessar o GitHub.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="b0a5f-224">Registre-se e configure um cliente padrão para outros fins.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="b0a5f-225">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b0a5f-226">Este padrão é semelhante ao pipeline de middleware de entrada da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="b0a5f-227">O padrão fornece um mecanismo para gerenciar preocupações transversais para solicitações HTTP, incluindo cache, manipulação de erros, serialização e registro.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="b0a5f-228">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="b0a5f-229">Gerencia o pooling e a `HttpClientHandler` vida útil das instâncias subjacentes para evitar problemas comuns de DNS que ocorrem ao gerenciar `HttpClient` vidas manualmente.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="b0a5f-230">Adiciona uma experiência de <xref:Microsoft.Extensions.Logging.ILogger> registro configurável através de todas as solicitações enviadas através de clientes criados pela fábrica.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b0a5f-231">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="b0a5f-232">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="b0a5f-232">Content root</span></span>

<span data-ttu-id="b0a5f-233">A raiz de conteúdo é o caminho base para:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-233">The content root is the base path for:</span></span>

* <span data-ttu-id="b0a5f-234">O executável hospedando o aplicativo (*.exe*).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="b0a5f-235">Conjuntos compilados que compõem o aplicativo (*.dll*).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="b0a5f-236">Arquivos de conteúdo usados pelo aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="b0a5f-237">Arquivos de navalha *(.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="b0a5f-238">Arquivos de configuração *(.json,* *.xml*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="b0a5f-239">Arquivos de dados *(.db)*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-239">Data files (*.db*)</span></span>
* <span data-ttu-id="b0a5f-240">A [raiz da Web](#web-root), tipicamente a pasta *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="b0a5f-241">Durante o desenvolvimento, a raiz de conteúdo é padrão para o diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="b0a5f-242">Este diretório também é o caminho base tanto para os arquivos de conteúdo do aplicativo quanto para a [raiz da Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="b0a5f-243">Especifique uma raiz de conteúdo diferente definindo seu caminho [ao construir o host](#host).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="b0a5f-244">Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/generic-host#contentrootpath-1).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentrootpath-1).</span></span>

## <a name="web-root"></a><span data-ttu-id="b0a5f-245">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="b0a5f-245">Web root</span></span>

<span data-ttu-id="b0a5f-246">A raiz da Web é o caminho base para arquivos de recursos públicos e estáticos, tais como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="b0a5f-247">Folhas de estilo *(.css)*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="b0a5f-248">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="b0a5f-249">Imagens (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="b0a5f-250">Por padrão, os arquivos estáticos são servidos apenas a partir do diretório raiz da Web e seus subdiretórios.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="b0a5f-251">O caminho raiz da Web é padrão para *{content root}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="b0a5f-252">Especifique uma raiz web diferente definindo seu caminho [ao construir o host](#host).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="b0a5f-253">Para obter mais informações, confira [Diretório base](xref:fundamentals/host/generic-host#webroot-1).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot-1).</span></span>

<span data-ttu-id="b0a5f-254">Impedir a publicação de arquivos em *wwwroot* com o [ \<item de projeto de> de conteúdo](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="b0a5f-255">O exemplo a seguir impede a publicação de conteúdo em *wwwroot/local* e seus subdiretórios:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="b0a5f-256">Em arquivos Razor *.cshtml,* tilde-slash ()`~/`aponta para a raiz da Web.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="b0a5f-257">Um caminho `~/` que começa é referido como um *caminho virtual.*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="b0a5f-258">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0a5f-259">Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="b0a5f-260">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="b0a5f-260">The Startup class</span></span>

<span data-ttu-id="b0a5f-261">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="b0a5f-262">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="b0a5f-263">O pipeline de tratamento de solicitação é definido.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="b0a5f-264">*Serviços* são componentes usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="b0a5f-265">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-265">For example, a logging component is a service.</span></span> <span data-ttu-id="b0a5f-266">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="b0a5f-267">O pipeline de movimentação de solicitações é composto como uma série de componentes de *middleware.*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="b0a5f-268">Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="b0a5f-269">Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="b0a5f-270">O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="b0a5f-271">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="b0a5f-272">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="b0a5f-273">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-273">Dependency injection (services)</span></span>

<span data-ttu-id="b0a5f-274">O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="b0a5f-275">Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="b0a5f-276">O parâmetro pode ser o tipo de serviço ou uma interface.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="b0a5f-277">O sistema de DI fornece o serviço em runtime.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="b0a5f-278">Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="b0a5f-279">A linha realçada é um exemplo de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="b0a5f-280">Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="b0a5f-281">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="b0a5f-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="b0a5f-282">Middleware</span></span>

<span data-ttu-id="b0a5f-283">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="b0a5f-284">Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="b0a5f-285">Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="b0a5f-286">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="b0a5f-287">O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="b0a5f-288">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="b0a5f-289">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="b0a5f-290">Host</span><span class="sxs-lookup"><span data-stu-id="b0a5f-290">Host</span></span>

<span data-ttu-id="b0a5f-291">Um aplicativo ASP.NET Core cria um *host* na inicialização.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="b0a5f-292">O host é um objeto que encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="b0a5f-293">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="b0a5f-293">An HTTP server implementation</span></span>
* <span data-ttu-id="b0a5f-294">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="b0a5f-294">Middleware components</span></span>
* <span data-ttu-id="b0a5f-295">Registro em log</span><span class="sxs-lookup"><span data-stu-id="b0a5f-295">Logging</span></span>
* <span data-ttu-id="b0a5f-296">DI</span><span class="sxs-lookup"><span data-stu-id="b0a5f-296">DI</span></span>
* <span data-ttu-id="b0a5f-297">Configuração</span><span class="sxs-lookup"><span data-stu-id="b0a5f-297">Configuration</span></span>

<span data-ttu-id="b0a5f-298">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="b0a5f-299">Dois hosts estão disponíveis: o Host Web e o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="b0a5f-300">No ASP.NET Core 2.x, o Host Genérico é somente para cenários não Web.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="b0a5f-301">O código para criar um host está em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="b0a5f-302">O método `CreateDefaultBuilder` configura um host com as opções usadas com frequência, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="b0a5f-303">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="b0a5f-304">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="b0a5f-305">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="b0a5f-306">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="b0a5f-307">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="b0a5f-307">Non-web scenarios</span></span>

<span data-ttu-id="b0a5f-308">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="b0a5f-309">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="b0a5f-310">Servidores</span><span class="sxs-lookup"><span data-stu-id="b0a5f-310">Servers</span></span>

<span data-ttu-id="b0a5f-311">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="b0a5f-312">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="b0a5f-313">Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="b0a5f-314">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="b0a5f-315">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="b0a5f-316">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="b0a5f-317">O Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="b0a5f-318">O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="b0a5f-319">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="b0a5f-320">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="b0a5f-321">macOS</span><span class="sxs-lookup"><span data-stu-id="b0a5f-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="b0a5f-322">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="b0a5f-323">O Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="b0a5f-324">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="b0a5f-325">Linux</span><span class="sxs-lookup"><span data-stu-id="b0a5f-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="b0a5f-326">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="b0a5f-327">O Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="b0a5f-328">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="b0a5f-329">Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="b0a5f-330">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="b0a5f-331">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="b0a5f-332">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="b0a5f-333">O Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="b0a5f-334">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="b0a5f-335">macOS</span><span class="sxs-lookup"><span data-stu-id="b0a5f-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="b0a5f-336">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="b0a5f-337">O Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="b0a5f-338">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="b0a5f-339">Linux</span><span class="sxs-lookup"><span data-stu-id="b0a5f-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="b0a5f-340">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="b0a5f-341">O Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="b0a5f-342">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0a5f-343">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="b0a5f-344">Configuração</span><span class="sxs-lookup"><span data-stu-id="b0a5f-344">Configuration</span></span>

<span data-ttu-id="b0a5f-345">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="b0a5f-346">Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="b0a5f-347">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="b0a5f-348">Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="b0a5f-349">Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="b0a5f-350">Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="b0a5f-351">Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b0a5f-352">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="b0a5f-353">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="b0a5f-354">Opções</span><span class="sxs-lookup"><span data-stu-id="b0a5f-354">Options</span></span>

<span data-ttu-id="b0a5f-355">Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="b0a5f-356">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="b0a5f-357">Por exemplo, o código a seguir define as opções de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="b0a5f-358">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="b0a5f-359">Ambientes</span><span class="sxs-lookup"><span data-stu-id="b0a5f-359">Environments</span></span>

<span data-ttu-id="b0a5f-360">Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="b0a5f-361">Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="b0a5f-362">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="b0a5f-363">O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="b0a5f-364">O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="b0a5f-365">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="b0a5f-366">Registro em log</span><span class="sxs-lookup"><span data-stu-id="b0a5f-366">Logging</span></span>

<span data-ttu-id="b0a5f-367">O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b0a5f-368">Provedores disponíveis incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-368">Available providers include the following:</span></span>

* <span data-ttu-id="b0a5f-369">Console</span><span class="sxs-lookup"><span data-stu-id="b0a5f-369">Console</span></span>
* <span data-ttu-id="b0a5f-370">Depurar</span><span class="sxs-lookup"><span data-stu-id="b0a5f-370">Debug</span></span>
* <span data-ttu-id="b0a5f-371">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="b0a5f-372">Log de eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="b0a5f-372">Windows Event Log</span></span>
* <span data-ttu-id="b0a5f-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b0a5f-373">TraceSource</span></span>
* <span data-ttu-id="b0a5f-374">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="b0a5f-374">Azure App Service</span></span>
* <span data-ttu-id="b0a5f-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="b0a5f-375">Azure Application Insights</span></span>

<span data-ttu-id="b0a5f-376">Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="b0a5f-377">Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="b0a5f-378">A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="b0a5f-379">Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="b0a5f-380">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b0a5f-381">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="b0a5f-382">Roteamento</span><span class="sxs-lookup"><span data-stu-id="b0a5f-382">Routing</span></span>

<span data-ttu-id="b0a5f-383">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="b0a5f-384">O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="b0a5f-385">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="b0a5f-386">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="b0a5f-387">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="b0a5f-387">Error handling</span></span>

<span data-ttu-id="b0a5f-388">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="b0a5f-389">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="b0a5f-389">A developer exception page</span></span>
* <span data-ttu-id="b0a5f-390">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="b0a5f-390">Custom error pages</span></span>
* <span data-ttu-id="b0a5f-391">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="b0a5f-391">Static status code pages</span></span>
* <span data-ttu-id="b0a5f-392">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="b0a5f-392">Startup exception handling</span></span>

<span data-ttu-id="b0a5f-393">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="b0a5f-394">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="b0a5f-394">Make HTTP requests</span></span>

<span data-ttu-id="b0a5f-395">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="b0a5f-396">O alocador:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-396">The factory:</span></span>

* <span data-ttu-id="b0a5f-397">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b0a5f-398">Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="b0a5f-399">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="b0a5f-400">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b0a5f-401">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="b0a5f-402">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="b0a5f-403">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="b0a5f-404">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b0a5f-405">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b0a5f-406">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="b0a5f-407">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="b0a5f-407">Content root</span></span>

<span data-ttu-id="b0a5f-408">A raiz de conteúdo é o caminho base para:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="b0a5f-409">Hospedável hospedando o aplicativo *(.exe).*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="b0a5f-410">Conjuntos compilados que compõem o aplicativo (*.dll*).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="b0a5f-411">Arquivos de conteúdo não-código usados pelo aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="b0a5f-412">Arquivos de navalha *(.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="b0a5f-413">Arquivos de configuração *(.json,* *.xml*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="b0a5f-414">Arquivos de dados *(.db)*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-414">Data files (*.db*)</span></span>
* <span data-ttu-id="b0a5f-415">[Raiz da Web](#web-root), tipicamente a pasta *wwwroot* publicada.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="b0a5f-416">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-416">During development:</span></span>

* <span data-ttu-id="b0a5f-417">A raiz de conteúdo é padrão para o diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="b0a5f-418">O diretório raiz do projeto é usado para criar o:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="b0a5f-419">Caminho para os arquivos de conteúdo não-código do aplicativo no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="b0a5f-420">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="b0a5f-421">Um caminho raiz de conteúdo alternativo pode ser especificado [ao construir o host](#host).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="b0a5f-422">Para obter mais informações, consulte <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="b0a5f-423">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="b0a5f-423">Web root</span></span>

<span data-ttu-id="b0a5f-424">A raiz da Web é o caminho base para arquivos de recursos públicos, não-código, estáticos, tais como:</span><span class="sxs-lookup"><span data-stu-id="b0a5f-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="b0a5f-425">Folhas de estilo *(.css)*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="b0a5f-426">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="b0a5f-427">Imagens (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="b0a5f-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="b0a5f-428">Os arquivos estáticos são servidos apenas por padrão do diretório raiz da Web (e subdiretórios).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="b0a5f-429">O caminho raiz da Web é padrão para *{content root}/wwwroot*, mas uma raiz web diferente pode ser especificada [ao construir o host](#host).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="b0a5f-430">Para obter mais informações, confira [Diretório base](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="b0a5f-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="b0a5f-431">Impedir a publicação de arquivos em *wwwroot* com o [ \<item de projeto de> de conteúdo](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="b0a5f-432">O exemplo a seguir impede a publicação de conteúdo no diretório e subdiretórios *locais wwwroot/local:*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="b0a5f-433">Nos arquivos Razor *(.cshtml),* o tilde-slash ()`~/`aponta para a raiz da web.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="b0a5f-434">Um caminho `~/` que começa é referido como um *caminho virtual.*</span><span class="sxs-lookup"><span data-stu-id="b0a5f-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="b0a5f-435">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="b0a5f-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
