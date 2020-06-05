---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84271894"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="9cbe6-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbe6-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="9cbe6-104">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)e [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="9cbe6-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9cbe6-105">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="9cbe6-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="9cbe6-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9cbe6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="9cbe6-108">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="9cbe6-108">Overview of dependency injection</span></span>

<span data-ttu-id="9cbe6-109">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="9cbe6-110">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="9cbe6-111">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="9cbe6-112">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="9cbe6-113">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="9cbe6-114">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="9cbe6-115">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="9cbe6-116">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="9cbe6-117">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="9cbe6-118">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="9cbe6-119">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="9cbe6-120">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="9cbe6-121">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="9cbe6-122">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="9cbe6-123">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="9cbe6-124">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="9cbe6-125">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="9cbe6-126">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="9cbe6-127">No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="9cbe6-128">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="9cbe6-129">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="9cbe6-130">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="9cbe6-131">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="9cbe6-132">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="9cbe6-133">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="9cbe6-134">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="9cbe6-135">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9cbe6-136">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="9cbe6-137">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="9cbe6-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="9cbe6-138">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="9cbe6-139">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="9cbe6-140">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="9cbe6-141">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="9cbe6-142">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="9cbe6-143">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="9cbe6-144">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="9cbe6-145">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="9cbe6-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="9cbe6-146">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="9cbe6-147">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="9cbe6-148">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="9cbe6-149">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="9cbe6-149">Services injected into Startup</span></span>

<span data-ttu-id="9cbe6-150">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="9cbe6-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="9cbe6-151">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9cbe6-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="9cbe6-152">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9cbe6-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="9cbe6-153">Framework-provided services</span></span>

<span data-ttu-id="9cbe6-154">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="9cbe6-155">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="9cbe6-156">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="9cbe6-157">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="9cbe6-158">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="9cbe6-158">Service Type</span></span> | <span data-ttu-id="9cbe6-159">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="9cbe6-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="9cbe6-160">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="9cbe6-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="9cbe6-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="9cbe6-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="9cbe6-164">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="9cbe6-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="9cbe6-166">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="9cbe6-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="9cbe6-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="9cbe6-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="9cbe6-170">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="9cbe6-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="9cbe6-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="9cbe6-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="9cbe6-174">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="9cbe6-174">Register additional services with extension methods</span></span>

<span data-ttu-id="9cbe6-175">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="9cbe6-176">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="9cbe6-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="9cbe6-177">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="9cbe6-178">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="9cbe6-178">Service lifetimes</span></span>

<span data-ttu-id="9cbe6-179">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="9cbe6-180">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="9cbe6-181">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-181">Transient</span></span>

<span data-ttu-id="9cbe6-182">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="9cbe6-183">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="9cbe6-184">Com escopo</span><span class="sxs-lookup"><span data-stu-id="9cbe6-184">Scoped</span></span>

<span data-ttu-id="9cbe6-185">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="9cbe6-186">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="9cbe6-187">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="9cbe6-188">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="9cbe6-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-189">Singleton</span></span>

<span data-ttu-id="9cbe6-190">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="9cbe6-191">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="9cbe6-192">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="9cbe6-193">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="9cbe6-194">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="9cbe6-195">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="9cbe6-196">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="9cbe6-196">Service registration methods</span></span>

<span data-ttu-id="9cbe6-197">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="9cbe6-198">Método</span><span class="sxs-lookup"><span data-stu-id="9cbe6-198">Method</span></span> | <span data-ttu-id="9cbe6-199">Automático</span><span class="sxs-lookup"><span data-stu-id="9cbe6-199">Automatic</span></span><br><span data-ttu-id="9cbe6-200">objeto</span><span class="sxs-lookup"><span data-stu-id="9cbe6-200">object</span></span><br><span data-ttu-id="9cbe6-201">descarte</span><span class="sxs-lookup"><span data-stu-id="9cbe6-201">disposal</span></span> | <span data-ttu-id="9cbe6-202">Vários</span><span class="sxs-lookup"><span data-stu-id="9cbe6-202">Multiple</span></span><br><span data-ttu-id="9cbe6-203">implementações</span><span class="sxs-lookup"><span data-stu-id="9cbe6-203">implementations</span></span> | <span data-ttu-id="9cbe6-204">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="9cbe6-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="9cbe6-205">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="9cbe6-206">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-206">Yes</span></span> | <span data-ttu-id="9cbe6-207">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-207">Yes</span></span> | <span data-ttu-id="9cbe6-208">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="9cbe6-209">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="9cbe6-210">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-210">Yes</span></span> | <span data-ttu-id="9cbe6-211">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-211">Yes</span></span> | <span data-ttu-id="9cbe6-212">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="9cbe6-213">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="9cbe6-214">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-214">Yes</span></span> | <span data-ttu-id="9cbe6-215">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-215">No</span></span> | <span data-ttu-id="9cbe6-216">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="9cbe6-217">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="9cbe6-218">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-218">No</span></span> | <span data-ttu-id="9cbe6-219">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-219">Yes</span></span> | <span data-ttu-id="9cbe6-220">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="9cbe6-221">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="9cbe6-222">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-222">No</span></span> | <span data-ttu-id="9cbe6-223">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-223">No</span></span> | <span data-ttu-id="9cbe6-224">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-224">Yes</span></span> |

<span data-ttu-id="9cbe6-225">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="9cbe6-226">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="9cbe6-227">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="9cbe6-228">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="9cbe6-229">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="9cbe6-230">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="9cbe6-231">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="9cbe6-232">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="9cbe6-233">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="9cbe6-234">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="9cbe6-235">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="9cbe6-236">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="9cbe6-237">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="9cbe6-238">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="9cbe6-238">Constructor injection behavior</span></span>

<span data-ttu-id="9cbe6-239">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="9cbe6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="9cbe6-241">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="9cbe6-242">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="9cbe6-243">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="9cbe6-244">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="9cbe6-245">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="9cbe6-246">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="9cbe6-246">Entity Framework contexts</span></span>

<span data-ttu-id="9cbe6-247">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="9cbe6-248">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="9cbe6-249">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="9cbe6-250">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="9cbe6-250">Lifetime and registration options</span></span>

<span data-ttu-id="9cbe6-251">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="9cbe6-252">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="9cbe6-253">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="9cbe6-254">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="9cbe6-255">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="9cbe6-256">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="9cbe6-257">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="9cbe6-258">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="9cbe6-259">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="9cbe6-260">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="9cbe6-261">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="9cbe6-262">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="9cbe6-263">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="9cbe6-264">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="9cbe6-265">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="9cbe6-266">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="9cbe6-267">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="9cbe6-268">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="9cbe6-269">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="9cbe6-270">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-270">**First request:**</span></span>

<span data-ttu-id="9cbe6-271">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-271">Controller operations:</span></span>

<span data-ttu-id="9cbe6-272">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="9cbe6-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="9cbe6-273">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="9cbe6-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="9cbe6-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-275">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-276">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-276">`OperationService` operations:</span></span>

<span data-ttu-id="9cbe6-277">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="9cbe6-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="9cbe6-278">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="9cbe6-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="9cbe6-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-280">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-281">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-281">**Second request:**</span></span>

<span data-ttu-id="9cbe6-282">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-282">Controller operations:</span></span>

<span data-ttu-id="9cbe6-283">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="9cbe6-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="9cbe6-284">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="9cbe6-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="9cbe6-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-286">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-287">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-287">`OperationService` operations:</span></span>

<span data-ttu-id="9cbe6-288">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="9cbe6-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="9cbe6-289">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="9cbe6-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="9cbe6-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-291">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-292">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="9cbe6-293">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-293">*Transient* objects are always different.</span></span> <span data-ttu-id="9cbe6-294">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="9cbe6-295">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="9cbe6-296">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="9cbe6-297">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="9cbe6-298">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="9cbe6-298">Call services from main</span></span>

<span data-ttu-id="9cbe6-299">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="9cbe6-300">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="9cbe6-301">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="9cbe6-302">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="9cbe6-302">Scope validation</span></span>

<span data-ttu-id="9cbe6-303">Quando o aplicativo está em execução no ambiente de desenvolvimento e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="9cbe6-304">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="9cbe6-305">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="9cbe6-306">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="9cbe6-307">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="9cbe6-308">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="9cbe6-309">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="9cbe6-310">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="9cbe6-311">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="9cbe6-312">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="9cbe6-312">Request Services</span></span>

<span data-ttu-id="9cbe6-313">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="9cbe6-314">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="9cbe6-315">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="9cbe6-316">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="9cbe6-317">Em vez disso, solicite os tipos que as classes exigem por meio de construtores de classe e permita que a estrutura Insira as dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="9cbe6-318">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="9cbe6-319">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="9cbe6-320">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="9cbe6-320">Design services for dependency injection</span></span>

<span data-ttu-id="9cbe6-321">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-321">Best practices are to:</span></span>

* <span data-ttu-id="9cbe6-322">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="9cbe6-323">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="9cbe6-324">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="9cbe6-325">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="9cbe6-326">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="9cbe6-327">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="9cbe6-328">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="9cbe6-329">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="9cbe6-330">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="9cbe6-331">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="9cbe6-332">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="9cbe6-332">Disposal of services</span></span>

<span data-ttu-id="9cbe6-333">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="9cbe6-334">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="9cbe6-335">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="9cbe6-336">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-336">In the following example:</span></span>

* <span data-ttu-id="9cbe6-337">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="9cbe6-338">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="9cbe6-339">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="9cbe6-340">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="9cbe6-341">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="9cbe6-341">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="9cbe6-342">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="9cbe6-342">Transient, limited lifetime</span></span>

<span data-ttu-id="9cbe6-343">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-343">**Scenario**</span></span>

<span data-ttu-id="9cbe6-344">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-344">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="9cbe6-345">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-345">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="9cbe6-346">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-346">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="9cbe6-347">**Solução**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-347">**Solution**</span></span>

<span data-ttu-id="9cbe6-348">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-348">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="9cbe6-349">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-349">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="9cbe6-350">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-350">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="9cbe6-351">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-351">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="9cbe6-352">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-352">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="9cbe6-353">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="9cbe6-353">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="9cbe6-354">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-354">**Scenario**</span></span>

<span data-ttu-id="9cbe6-355">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-355">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="9cbe6-356">**Solução**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-356">**Solution**</span></span>

<span data-ttu-id="9cbe6-357">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-357">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="9cbe6-358">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-358">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="9cbe6-359">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-359">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="9cbe6-360">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-360">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="9cbe6-361">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="9cbe6-361">General Guidelines</span></span>

* <span data-ttu-id="9cbe6-362">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-362">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="9cbe6-363">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-363">Use the factory pattern instead.</span></span>
* <span data-ttu-id="9cbe6-364">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-364">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="9cbe6-365">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-365">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="9cbe6-366">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-366">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="9cbe6-367">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-367">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="9cbe6-368">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-368">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="9cbe6-369">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-369">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="9cbe6-370">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="9cbe6-370">Default service container replacement</span></span>

<span data-ttu-id="9cbe6-371">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-371">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="9cbe6-372">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-372">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="9cbe6-373">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="9cbe6-373">Property injection</span></span>
* <span data-ttu-id="9cbe6-374">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="9cbe6-374">Injection based on name</span></span>
* <span data-ttu-id="9cbe6-375">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="9cbe6-375">Child containers</span></span>
* <span data-ttu-id="9cbe6-376">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="9cbe6-376">Custom lifetime management</span></span>
* <span data-ttu-id="9cbe6-377">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="9cbe6-377">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="9cbe6-378">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="9cbe6-378">Convention-based registration</span></span>

<span data-ttu-id="9cbe6-379">Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-379">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="9cbe6-380">Autofac</span><span class="sxs-lookup"><span data-stu-id="9cbe6-380">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="9cbe6-381">DryIoc</span><span class="sxs-lookup"><span data-stu-id="9cbe6-381">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="9cbe6-382">Grace</span><span class="sxs-lookup"><span data-stu-id="9cbe6-382">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="9cbe6-383">LightInject</span><span class="sxs-lookup"><span data-stu-id="9cbe6-383">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="9cbe6-384">Lamar</span><span class="sxs-lookup"><span data-stu-id="9cbe6-384">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="9cbe6-385">Stashbox</span><span class="sxs-lookup"><span data-stu-id="9cbe6-385">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="9cbe6-386">Unity</span><span class="sxs-lookup"><span data-stu-id="9cbe6-386">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="9cbe6-387">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="9cbe6-387">Thread safety</span></span>

<span data-ttu-id="9cbe6-388">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-388">Create thread-safe singleton services.</span></span> <span data-ttu-id="9cbe6-389">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-389">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="9cbe6-390">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-390">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="9cbe6-391">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-391">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="9cbe6-392">Recomendações</span><span class="sxs-lookup"><span data-stu-id="9cbe6-392">Recommendations</span></span>

* <span data-ttu-id="9cbe6-393">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-393">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="9cbe6-394">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-394">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="9cbe6-395">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-395">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="9cbe6-396">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-396">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="9cbe6-397">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-397">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="9cbe6-398">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-398">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="9cbe6-399">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-399">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="9cbe6-400">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-400">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="9cbe6-401">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-401">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="9cbe6-402">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-402">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="9cbe6-403">**Incorreto**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-403">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="9cbe6-404">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-404">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="9cbe6-405">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-405">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="9cbe6-406">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-406">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="9cbe6-407">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-407">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="9cbe6-408">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-408">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="9cbe6-409">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-409">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="9cbe6-410">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-410">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="9cbe6-411">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-411">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="9cbe6-412">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="9cbe6-412">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="9cbe6-413">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornece multilocação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="9cbe6-414">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-414">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="9cbe6-415">Consulte os aplicativos de exemplos em https://github.com/OrchardCMS/OrchardCore.Samples para obter exemplos de como criar aplicativos modulares e multilocatários usando o Orchard Core Framework sem nenhum dos recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-415">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9cbe6-416">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9cbe6-416">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="9cbe6-417">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbe6-417">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="9cbe6-418">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="9cbe6-418">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="9cbe6-419">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="9cbe6-419">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="9cbe6-420">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="9cbe6-420">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="9cbe6-421">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbe6-421">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9cbe6-422">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-422">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="9cbe6-423">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-423">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="9cbe6-424">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9cbe6-424">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="9cbe6-425">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="9cbe6-425">Overview of dependency injection</span></span>

<span data-ttu-id="9cbe6-426">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-426">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="9cbe6-427">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-427">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="9cbe6-428">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-428">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="9cbe6-429">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-429">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="9cbe6-430">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-430">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="9cbe6-431">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-431">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="9cbe6-432">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-432">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="9cbe6-433">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-433">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="9cbe6-434">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-434">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="9cbe6-435">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-435">This implementation is difficult to unit test.</span></span> <span data-ttu-id="9cbe6-436">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-436">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="9cbe6-437">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-437">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="9cbe6-438">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-438">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="9cbe6-439">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-439">Registration of the dependency in a service container.</span></span> <span data-ttu-id="9cbe6-440">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-440">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="9cbe6-441">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-441">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="9cbe6-442">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-442">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="9cbe6-443">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-443">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="9cbe6-444">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-444">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="9cbe6-445">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-445">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="9cbe6-446">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-446">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="9cbe6-447">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-447">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="9cbe6-448">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-448">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="9cbe6-449">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-449">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="9cbe6-450">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-450">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="9cbe6-451">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-451">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="9cbe6-452">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-452">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9cbe6-453">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-453">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="9cbe6-454">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="9cbe6-454">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="9cbe6-455">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-455">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="9cbe6-456">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-456">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="9cbe6-457">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-457">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="9cbe6-458">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-458">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="9cbe6-459">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-459">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="9cbe6-460">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-460">We recommended that apps follow this convention.</span></span> <span data-ttu-id="9cbe6-461">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-461">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="9cbe6-462">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="9cbe6-462">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="9cbe6-463">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-463">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="9cbe6-464">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-464">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="9cbe6-465">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-465">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="9cbe6-466">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="9cbe6-466">Services injected into Startup</span></span>

<span data-ttu-id="9cbe6-467">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="9cbe6-467">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="9cbe6-468">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9cbe6-468">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="9cbe6-469">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-469">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9cbe6-470">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="9cbe6-470">Framework-provided services</span></span>

<span data-ttu-id="9cbe6-471">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-471">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="9cbe6-472">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-472">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="9cbe6-473">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-473">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="9cbe6-474">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-474">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="9cbe6-475">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="9cbe6-475">Service Type</span></span> | <span data-ttu-id="9cbe6-476">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="9cbe6-476">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="9cbe6-477">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-477">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="9cbe6-478">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-478">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="9cbe6-479">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-479">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="9cbe6-480">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-480">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="9cbe6-481">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-481">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="9cbe6-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="9cbe6-483">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-483">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="9cbe6-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="9cbe6-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="9cbe6-486">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-486">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="9cbe6-487">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-487">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="9cbe6-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-488">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="9cbe6-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="9cbe6-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="9cbe6-491">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="9cbe6-491">Register additional services with extension methods</span></span>

<span data-ttu-id="9cbe6-492">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-492">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="9cbe6-493">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="9cbe6-493">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="9cbe6-494">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-494">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="9cbe6-495">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="9cbe6-495">Service lifetimes</span></span>

<span data-ttu-id="9cbe6-496">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-496">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="9cbe6-497">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-497">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="9cbe6-498">Transitório</span><span class="sxs-lookup"><span data-stu-id="9cbe6-498">Transient</span></span>

<span data-ttu-id="9cbe6-499">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-499">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="9cbe6-500">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-500">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="9cbe6-501">Com escopo</span><span class="sxs-lookup"><span data-stu-id="9cbe6-501">Scoped</span></span>

<span data-ttu-id="9cbe6-502">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-502">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="9cbe6-503">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-503">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="9cbe6-504">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-504">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="9cbe6-505">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-505">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="9cbe6-506">Singleton</span><span class="sxs-lookup"><span data-stu-id="9cbe6-506">Singleton</span></span>

<span data-ttu-id="9cbe6-507">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-507">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="9cbe6-508">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-508">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="9cbe6-509">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-509">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="9cbe6-510">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-510">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="9cbe6-511">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-511">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="9cbe6-512">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-512">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="9cbe6-513">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="9cbe6-513">Service registration methods</span></span>

<span data-ttu-id="9cbe6-514">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-514">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="9cbe6-515">Método</span><span class="sxs-lookup"><span data-stu-id="9cbe6-515">Method</span></span> | <span data-ttu-id="9cbe6-516">Automático</span><span class="sxs-lookup"><span data-stu-id="9cbe6-516">Automatic</span></span><br><span data-ttu-id="9cbe6-517">objeto</span><span class="sxs-lookup"><span data-stu-id="9cbe6-517">object</span></span><br><span data-ttu-id="9cbe6-518">descarte</span><span class="sxs-lookup"><span data-stu-id="9cbe6-518">disposal</span></span> | <span data-ttu-id="9cbe6-519">Vários</span><span class="sxs-lookup"><span data-stu-id="9cbe6-519">Multiple</span></span><br><span data-ttu-id="9cbe6-520">implementações</span><span class="sxs-lookup"><span data-stu-id="9cbe6-520">implementations</span></span> | <span data-ttu-id="9cbe6-521">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="9cbe6-521">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="9cbe6-522">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-522">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="9cbe6-523">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-523">Yes</span></span> | <span data-ttu-id="9cbe6-524">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-524">Yes</span></span> | <span data-ttu-id="9cbe6-525">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-525">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="9cbe6-526">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-526">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="9cbe6-527">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-527">Yes</span></span> | <span data-ttu-id="9cbe6-528">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-528">Yes</span></span> | <span data-ttu-id="9cbe6-529">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="9cbe6-530">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-530">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="9cbe6-531">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-531">Yes</span></span> | <span data-ttu-id="9cbe6-532">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-532">No</span></span> | <span data-ttu-id="9cbe6-533">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-533">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="9cbe6-534">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="9cbe6-535">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-535">No</span></span> | <span data-ttu-id="9cbe6-536">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-536">Yes</span></span> | <span data-ttu-id="9cbe6-537">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-537">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="9cbe6-538">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-538">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="9cbe6-539">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-539">No</span></span> | <span data-ttu-id="9cbe6-540">Não</span><span class="sxs-lookup"><span data-stu-id="9cbe6-540">No</span></span> | <span data-ttu-id="9cbe6-541">Sim</span><span class="sxs-lookup"><span data-stu-id="9cbe6-541">Yes</span></span> |

<span data-ttu-id="9cbe6-542">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-542">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="9cbe6-543">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-543">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="9cbe6-544">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-544">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="9cbe6-545">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-545">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="9cbe6-546">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-546">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="9cbe6-547">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-547">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="9cbe6-548">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-548">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="9cbe6-549">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-549">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="9cbe6-550">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-550">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="9cbe6-551">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-551">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="9cbe6-552">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-552">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="9cbe6-553">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-553">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="9cbe6-554">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-554">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="9cbe6-555">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="9cbe6-555">Constructor injection behavior</span></span>

<span data-ttu-id="9cbe6-556">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-556">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="9cbe6-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="9cbe6-558">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-558">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="9cbe6-559">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-559">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="9cbe6-560">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-560">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="9cbe6-561">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-561">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="9cbe6-562">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-562">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="9cbe6-563">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="9cbe6-563">Entity Framework contexts</span></span>

<span data-ttu-id="9cbe6-564">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-564">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="9cbe6-565">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-565">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="9cbe6-566">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-566">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="9cbe6-567">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="9cbe6-567">Lifetime and registration options</span></span>

<span data-ttu-id="9cbe6-568">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-568">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="9cbe6-569">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-569">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="9cbe6-570">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-570">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="9cbe6-571">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-571">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="9cbe6-572">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-572">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="9cbe6-573">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-573">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="9cbe6-574">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-574">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="9cbe6-575">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-575">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="9cbe6-576">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-576">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="9cbe6-577">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-577">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="9cbe6-578">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-578">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="9cbe6-579">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-579">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="9cbe6-580">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-580">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="9cbe6-581">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-581">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="9cbe6-582">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-582">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="9cbe6-583">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-583">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="9cbe6-584">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-584">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="9cbe6-585">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-585">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="9cbe6-586">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-586">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="9cbe6-587">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-587">**First request:**</span></span>

<span data-ttu-id="9cbe6-588">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-588">Controller operations:</span></span>

<span data-ttu-id="9cbe6-589">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="9cbe6-589">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="9cbe6-590">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="9cbe6-590">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="9cbe6-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-592">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-592">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-593">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-593">`OperationService` operations:</span></span>

<span data-ttu-id="9cbe6-594">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="9cbe6-594">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="9cbe6-595">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="9cbe6-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="9cbe6-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-597">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-598">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-598">**Second request:**</span></span>

<span data-ttu-id="9cbe6-599">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-599">Controller operations:</span></span>

<span data-ttu-id="9cbe6-600">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="9cbe6-600">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="9cbe6-601">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="9cbe6-601">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="9cbe6-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-603">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-604">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-604">`OperationService` operations:</span></span>

<span data-ttu-id="9cbe6-605">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="9cbe6-605">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="9cbe6-606">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="9cbe6-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="9cbe6-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="9cbe6-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="9cbe6-608">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="9cbe6-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="9cbe6-609">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-609">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="9cbe6-610">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-610">*Transient* objects are always different.</span></span> <span data-ttu-id="9cbe6-611">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-611">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="9cbe6-612">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-612">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="9cbe6-613">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-613">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="9cbe6-614">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-614">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="9cbe6-615">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="9cbe6-615">Call services from main</span></span>

<span data-ttu-id="9cbe6-616">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-616">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="9cbe6-617">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-617">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="9cbe6-618">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-618">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="9cbe6-619">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="9cbe6-619">Scope validation</span></span>

<span data-ttu-id="9cbe6-620">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-620">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="9cbe6-621">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-621">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="9cbe6-622">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-622">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="9cbe6-623">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-623">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="9cbe6-624">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-624">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="9cbe6-625">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-625">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="9cbe6-626">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-626">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="9cbe6-627">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-627">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="9cbe6-628">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-628">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="9cbe6-629">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="9cbe6-629">Request Services</span></span>

<span data-ttu-id="9cbe6-630">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-630">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="9cbe6-631">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-631">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="9cbe6-632">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-632">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="9cbe6-633">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-633">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="9cbe6-634">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-634">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="9cbe6-635">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-635">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="9cbe6-636">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-636">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="9cbe6-637">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="9cbe6-637">Design services for dependency injection</span></span>

<span data-ttu-id="9cbe6-638">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-638">Best practices are to:</span></span>

* <span data-ttu-id="9cbe6-639">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-639">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="9cbe6-640">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-640">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="9cbe6-641">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-641">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="9cbe6-642">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-642">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="9cbe6-643">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-643">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="9cbe6-644">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-644">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="9cbe6-645">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-645">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="9cbe6-646">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-646">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="9cbe6-647">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-647">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="9cbe6-648">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-648">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="9cbe6-649">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="9cbe6-649">Disposal of services</span></span>

<span data-ttu-id="9cbe6-650">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-650">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="9cbe6-651">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-651">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="9cbe6-652">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-652">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="9cbe6-653">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-653">In the following example:</span></span>

* <span data-ttu-id="9cbe6-654">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-654">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="9cbe6-655">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-655">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="9cbe6-656">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-656">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="9cbe6-657">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-657">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="9cbe6-658">Diretrizes de IDisposable para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="9cbe6-658">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="9cbe6-659">Tempo de vida transitório, limitado</span><span class="sxs-lookup"><span data-stu-id="9cbe6-659">Transient, limited lifetime</span></span>

<span data-ttu-id="9cbe6-660">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-660">**Scenario**</span></span>

<span data-ttu-id="9cbe6-661">O aplicativo requer uma <xref:System.IDisposable> instância com um tempo de vida transitório para qualquer um dos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-661">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="9cbe6-662">A instância é resolvida no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-662">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="9cbe6-663">A instância deve ser descartada antes do término do escopo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-663">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="9cbe6-664">**Solução**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-664">**Solution**</span></span>

<span data-ttu-id="9cbe6-665">Use o padrão de fábrica para criar uma instância fora do escopo pai.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-665">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="9cbe6-666">Nessa situação, o aplicativo geralmente teria um `Create` método que chamasse o construtor do tipo final diretamente.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-666">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="9cbe6-667">Se o tipo final tiver outras dependências, a fábrica poderá:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-667">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="9cbe6-668">Receber um <xref:System.IServiceProvider> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-668">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="9cbe6-669">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para instanciar a instância fora do contêiner, ao usar o contêiner para suas dependências.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-669">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="9cbe6-670">Instância compartilhada, tempo de vida limitado</span><span class="sxs-lookup"><span data-stu-id="9cbe6-670">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="9cbe6-671">**Cenário**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-671">**Scenario**</span></span>

<span data-ttu-id="9cbe6-672">O aplicativo requer uma <xref:System.IDisposable> instância compartilhada entre vários serviços, mas o <xref:System.IDisposable> deve ter um tempo de vida limitado.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-672">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="9cbe6-673">**Solução**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-673">**Solution**</span></span>

<span data-ttu-id="9cbe6-674">Registre a instância com um tempo de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-674">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="9cbe6-675">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar e criar um novo <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-675">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="9cbe6-676">Use o escopo <xref:System.IServiceProvider> para obter os serviços necessários.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-676">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="9cbe6-677">Descarte o escopo quando o tempo de vida deve terminar.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-677">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="9cbe6-678">Diretrizes gerais</span><span class="sxs-lookup"><span data-stu-id="9cbe6-678">General Guidelines</span></span>

* <span data-ttu-id="9cbe6-679">Não registre <xref:System.IDisposable> instâncias com um escopo transitório.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-679">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="9cbe6-680">Em vez disso, use o padrão de fábrica.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-680">Use the factory pattern instead.</span></span>
* <span data-ttu-id="9cbe6-681">Não resolva instâncias transitórias ou com escopo definido <xref:System.IDisposable> no escopo raiz.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-681">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="9cbe6-682">A única exceção geral é quando o aplicativo cria/recria e descarta o <xref:System.IServiceProvider> , que não é um padrão ideal.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-682">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="9cbe6-683">O recebimento de uma <xref:System.IDisposable> dependência por meio de di não exige que o receptor se implemente <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-683">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="9cbe6-684">O receptor da <xref:System.IDisposable> dependência não deve chamar <xref:System.IDisposable.Dispose%2A> essa dependência.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-684">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="9cbe6-685">Os escopos devem ser usados para controlar o tempo de vida dos serviços.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-685">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="9cbe6-686">Os escopos não são hierárquicos e não há nenhuma conexão especial entre escopos.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-686">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="9cbe6-687">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="9cbe6-687">Default service container replacement</span></span>

<span data-ttu-id="9cbe6-688">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-688">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="9cbe6-689">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-689">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="9cbe6-690">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="9cbe6-690">Property injection</span></span>
* <span data-ttu-id="9cbe6-691">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="9cbe6-691">Injection based on name</span></span>
* <span data-ttu-id="9cbe6-692">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="9cbe6-692">Child containers</span></span>
* <span data-ttu-id="9cbe6-693">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="9cbe6-693">Custom lifetime management</span></span>
* <span data-ttu-id="9cbe6-694">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="9cbe6-694">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="9cbe6-695">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="9cbe6-695">Convention-based registration</span></span>

<span data-ttu-id="9cbe6-696">Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-696">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="9cbe6-697">Autofac</span><span class="sxs-lookup"><span data-stu-id="9cbe6-697">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="9cbe6-698">DryIoc</span><span class="sxs-lookup"><span data-stu-id="9cbe6-698">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="9cbe6-699">Grace</span><span class="sxs-lookup"><span data-stu-id="9cbe6-699">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="9cbe6-700">LightInject</span><span class="sxs-lookup"><span data-stu-id="9cbe6-700">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="9cbe6-701">Lamar</span><span class="sxs-lookup"><span data-stu-id="9cbe6-701">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="9cbe6-702">Stashbox</span><span class="sxs-lookup"><span data-stu-id="9cbe6-702">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="9cbe6-703">Unity</span><span class="sxs-lookup"><span data-stu-id="9cbe6-703">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="9cbe6-704">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="9cbe6-704">Thread safety</span></span>

<span data-ttu-id="9cbe6-705">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-705">Create thread-safe singleton services.</span></span> <span data-ttu-id="9cbe6-706">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-706">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="9cbe6-707">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-707">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="9cbe6-708">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-708">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="9cbe6-709">Recomendações</span><span class="sxs-lookup"><span data-stu-id="9cbe6-709">Recommendations</span></span>

* <span data-ttu-id="9cbe6-710">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-710">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="9cbe6-711">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-711">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="9cbe6-712">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-712">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="9cbe6-713">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-713">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="9cbe6-714">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-714">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="9cbe6-715">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-715">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="9cbe6-716">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-716">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="9cbe6-717">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-717">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="9cbe6-718">Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-718">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="9cbe6-719">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-719">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="9cbe6-720">**Incorreto**</span><span class="sxs-lookup"><span data-stu-id="9cbe6-720">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="9cbe6-721">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="9cbe6-721">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="9cbe6-722">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="9cbe6-722">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="9cbe6-723">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="9cbe6-723">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="9cbe6-724">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-724">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="9cbe6-725">As exceções são raras, principalmente os casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-725">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="9cbe6-726">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-726">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="9cbe6-727">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="9cbe6-727">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9cbe6-728">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9cbe6-728">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="9cbe6-729">Quatro maneiras de descartar IDisposables em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbe6-729">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="9cbe6-730">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="9cbe6-730">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="9cbe6-731">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="9cbe6-731">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="9cbe6-732">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="9cbe6-732">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="9cbe6-733">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9cbe6-733">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
