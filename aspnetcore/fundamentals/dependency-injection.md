---
title: Injeção de dependência no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 943ea30c2e4887638f69b6dcdb7e323bcee40240
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405972"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="79596-103">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79596-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="79596-104">Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="79596-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79596-105">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="79596-106">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="79596-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="79596-107">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79596-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="79596-108">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="79596-108">Overview of dependency injection</span></span>

<span data-ttu-id="79596-109">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="79596-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="79596-110">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="79596-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="79596-111">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="79596-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="79596-112">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="79596-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="79596-113">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="79596-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="79596-114">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="79596-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="79596-115">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="79596-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="79596-116">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="79596-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="79596-117">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="79596-118">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="79596-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="79596-119">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="79596-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="79596-120">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="79596-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="79596-121">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="79596-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="79596-122">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="79596-123">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="79596-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="79596-124">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="79596-125">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="79596-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="79596-126">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="79596-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="79596-127">No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="79596-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="79596-128">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="79596-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="79596-129">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="79596-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="79596-130">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="79596-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="79596-131">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="79596-132">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="79596-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="79596-133">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="79596-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="79596-134">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="79596-135">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="79596-136">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="79596-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="79596-137">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="79596-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="79596-138">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="79596-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="79596-139">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="79596-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="79596-140">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="79596-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="79596-141">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="79596-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="79596-142">Por exemplo, `services.AddMvc()` adiciona os serviços exigidos pelo MVC e por Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="79596-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="79596-143">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="79596-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="79596-144">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="79596-145">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="79596-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="79596-146">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="79596-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="79596-147">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="79596-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="79596-148">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="79596-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="79596-149">Serviços injetados em Startup</span><span class="sxs-lookup"><span data-stu-id="79596-149">Services injected into Startup</span></span>

<span data-ttu-id="79596-150">Somente os seguintes tipos de `Startup` serviço podem ser injetados<xref:Microsoft.Extensions.Hosting.IHostBuilder>no construtor ao usar o Host Genérico ( ):</span><span class="sxs-lookup"><span data-stu-id="79596-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="79596-151">Os serviços podem `Startup.Configure`ser injetados em:</span><span class="sxs-lookup"><span data-stu-id="79596-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="79596-152">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="79596-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="79596-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="79596-153">Framework-provided services</span></span>

<span data-ttu-id="79596-154">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos da plataforma, como Entity Framework Core e ASP.NET MVC Core.</span><span class="sxs-lookup"><span data-stu-id="79596-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="79596-155">Inicialmente, `IServiceCollection` o `ConfigureServices` fornecido tem serviços definidos pelo framework, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="79596-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="79596-156">Não é incomum que um aplicativo baseado em um modelo ASP.NET Core tenha centenas de serviços registrados pela framework.</span><span class="sxs-lookup"><span data-stu-id="79596-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="79596-157">Uma pequena amostra de serviços registrados em quadro está listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="79596-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="79596-158">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="79596-158">Service Type</span></span> | <span data-ttu-id="79596-159">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="79596-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="79596-160">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="79596-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="79596-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="79596-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="79596-164">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="79596-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="79596-166">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="79596-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="79596-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="79596-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="79596-170">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="79596-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="79596-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="79596-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="79596-174">Registre serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="79596-174">Register additional services with extension methods</span></span>

<span data-ttu-id="79596-175">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="79596-176">O código a seguir é um exemplo de como adicionar serviços adicionais ao <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>contêiner usando os métodos de extensão [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e :</span><span class="sxs-lookup"><span data-stu-id="79596-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="79596-177">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="79596-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="79596-178">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="79596-178">Service lifetimes</span></span>

<span data-ttu-id="79596-179">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="79596-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="79596-180">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="79596-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="79596-181">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-181">Transient</span></span>

<span data-ttu-id="79596-182">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="79596-183">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="79596-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="79596-184">Com escopo</span><span class="sxs-lookup"><span data-stu-id="79596-184">Scoped</span></span>

<span data-ttu-id="79596-185">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="79596-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="79596-186">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="79596-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="79596-187">Não injete por meio de injeção de construtor porque isso força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="79596-187">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="79596-188">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="79596-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="79596-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-189">Singleton</span></span>

<span data-ttu-id="79596-190">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="79596-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="79596-191">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="79596-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="79596-192">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="79596-193">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="79596-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="79596-194">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="79596-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="79596-195">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="79596-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="79596-196">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="79596-196">Service registration methods</span></span>

<span data-ttu-id="79596-197">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="79596-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="79596-198">Método</span><span class="sxs-lookup"><span data-stu-id="79596-198">Method</span></span> | <span data-ttu-id="79596-199">Automático</span><span class="sxs-lookup"><span data-stu-id="79596-199">Automatic</span></span><br><span data-ttu-id="79596-200">objeto</span><span class="sxs-lookup"><span data-stu-id="79596-200">object</span></span><br><span data-ttu-id="79596-201">descarte</span><span class="sxs-lookup"><span data-stu-id="79596-201">disposal</span></span> | <span data-ttu-id="79596-202">Vários</span><span class="sxs-lookup"><span data-stu-id="79596-202">Multiple</span></span><br><span data-ttu-id="79596-203">implementações</span><span class="sxs-lookup"><span data-stu-id="79596-203">implementations</span></span> | <span data-ttu-id="79596-204">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="79596-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="79596-205">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="79596-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="79596-206">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-206">Yes</span></span> | <span data-ttu-id="79596-207">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-207">Yes</span></span> | <span data-ttu-id="79596-208">Não</span><span class="sxs-lookup"><span data-stu-id="79596-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="79596-209">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="79596-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="79596-210">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-210">Yes</span></span> | <span data-ttu-id="79596-211">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-211">Yes</span></span> | <span data-ttu-id="79596-212">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="79596-213">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="79596-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="79596-214">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-214">Yes</span></span> | <span data-ttu-id="79596-215">Não</span><span class="sxs-lookup"><span data-stu-id="79596-215">No</span></span> | <span data-ttu-id="79596-216">Não</span><span class="sxs-lookup"><span data-stu-id="79596-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="79596-217">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="79596-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="79596-218">Não</span><span class="sxs-lookup"><span data-stu-id="79596-218">No</span></span> | <span data-ttu-id="79596-219">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-219">Yes</span></span> | <span data-ttu-id="79596-220">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="79596-221">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="79596-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="79596-222">Não</span><span class="sxs-lookup"><span data-stu-id="79596-222">No</span></span> | <span data-ttu-id="79596-223">Não</span><span class="sxs-lookup"><span data-stu-id="79596-223">No</span></span> | <span data-ttu-id="79596-224">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-224">Yes</span></span> |

<span data-ttu-id="79596-225">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="79596-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="79596-226">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="79596-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="79596-227">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="79596-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="79596-228">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="79596-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="79596-229">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="79596-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="79596-230">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="79596-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="79596-231">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="79596-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="79596-232">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="79596-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="79596-233">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="79596-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="79596-234">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="79596-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="79596-235">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="79596-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="79596-236">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="79596-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="79596-237">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="79596-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="79596-238">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="79596-238">Constructor injection behavior</span></span>

<span data-ttu-id="79596-239">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="79596-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="79596-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Permite a criação de objetos sem registro de serviço no recipiente de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="79596-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="79596-241">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="79596-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="79596-242">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="79596-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="79596-243">Quando os serviços são resolvidos por `IServiceProvider` ou `ActivatorUtilities`, a injeção do construtor exige um construtor *público*.</span><span class="sxs-lookup"><span data-stu-id="79596-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="79596-244">Quando os serviços são resolvidos por `ActivatorUtilities`, a injeção de construtor exige a existência de apenas de um construtor aplicável.</span><span class="sxs-lookup"><span data-stu-id="79596-244">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="79596-245">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="79596-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="79596-246">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="79596-246">Entity Framework contexts</span></span>

<span data-ttu-id="79596-247">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="79596-248">O tempo de vida padrão terá escopo se um tempo de vida não for especificado por uma sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="79596-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="79596-249">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="79596-250">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="79596-250">Lifetime and registration options</span></span>

<span data-ttu-id="79596-251">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="79596-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="79596-252">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="79596-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="79596-253">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="79596-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="79596-254">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="79596-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="79596-255">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="79596-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="79596-256">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="79596-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="79596-257">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="79596-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="79596-258">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="79596-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="79596-259">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="79596-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="79596-260">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="79596-261">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="79596-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="79596-262">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="79596-263">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="79596-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="79596-264">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="79596-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="79596-265">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="79596-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="79596-266">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="79596-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="79596-267">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="79596-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="79596-268">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="79596-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="79596-269">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="79596-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="79596-270">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="79596-270">**First request:**</span></span>

<span data-ttu-id="79596-271">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="79596-271">Controller operations:</span></span>

<span data-ttu-id="79596-272">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="79596-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="79596-273">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="79596-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="79596-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-275">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-276">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="79596-276">`OperationService` operations:</span></span>

<span data-ttu-id="79596-277">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="79596-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="79596-278">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="79596-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="79596-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-280">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-281">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="79596-281">**Second request:**</span></span>

<span data-ttu-id="79596-282">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="79596-282">Controller operations:</span></span>

<span data-ttu-id="79596-283">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="79596-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="79596-284">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="79596-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="79596-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-286">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-287">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="79596-287">`OperationService` operations:</span></span>

<span data-ttu-id="79596-288">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="79596-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="79596-289">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="79596-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="79596-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-291">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-292">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="79596-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="79596-293">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="79596-293">*Transient* objects are always different.</span></span> <span data-ttu-id="79596-294">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="79596-295">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="79596-296">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="79596-297">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="79596-298">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="79596-298">Call services from main</span></span>

<span data-ttu-id="79596-299">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="79596-300">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="79596-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="79596-301">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="79596-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="79596-302">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="79596-302">Scope validation</span></span>

<span data-ttu-id="79596-303">Quando o aplicativo está sendo executado no ambiente Desenvolvimento e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviços padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="79596-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="79596-304">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="79596-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="79596-305">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="79596-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="79596-306">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="79596-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="79596-307">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="79596-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="79596-308">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="79596-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="79596-309">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="79596-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="79596-310">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="79596-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="79596-311">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="79596-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="79596-312">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="79596-312">Request Services</span></span>

<span data-ttu-id="79596-313">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="79596-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="79596-314">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="79596-315">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="79596-316">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="79596-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="79596-317">Em vez disso, solicite os tipos que as classes exigem através de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="79596-318">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="79596-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="79596-319">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="79596-320">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="79596-320">Design services for dependency injection</span></span>

<span data-ttu-id="79596-321">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="79596-321">Best practices are to:</span></span>

* <span data-ttu-id="79596-322">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="79596-323">Evite classes e membros estaduais e estáticos.</span><span class="sxs-lookup"><span data-stu-id="79596-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="79596-324">Designe aplicativos para usar serviços de singleton, que evitam criar estado global.</span><span class="sxs-lookup"><span data-stu-id="79596-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="79596-325">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="79596-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="79596-326">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="79596-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="79596-327">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="79596-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="79596-328">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="79596-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="79596-329">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="79596-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="79596-330">Lembre-se de que as classes de modelo de página de Razor Pages e as classes do controlador de MVC devem se concentrar em questões de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="79596-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="79596-331">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="79596-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="79596-332">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="79596-332">Disposal of services</span></span>

<span data-ttu-id="79596-333">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="79596-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="79596-334">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="79596-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="79596-335">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="79596-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="79596-336">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="79596-336">In the following example:</span></span>

* <span data-ttu-id="79596-337">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="79596-338">As vidas de serviço pretendidas não são conhecidas pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="79596-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="79596-339">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="79596-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="79596-340">Se os serviços não estiverem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="79596-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="79596-341">Para uma discussão sobre as opções de descarte de [serviços, consulte Quatro maneiras de descartar IDisposables em ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="79596-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="79596-342">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="79596-342">Default service container replacement</span></span>

<span data-ttu-id="79596-343">O contêiner de serviço embutido foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumo.</span><span class="sxs-lookup"><span data-stu-id="79596-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="79596-344">Recomendamos o uso do contêiner embutido, a menos que você precise de um recurso específico que o contêiner embutido não suporte, tais como:</span><span class="sxs-lookup"><span data-stu-id="79596-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="79596-345">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="79596-345">Property injection</span></span>
* <span data-ttu-id="79596-346">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="79596-346">Injection based on name</span></span>
* <span data-ttu-id="79596-347">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="79596-347">Child containers</span></span>
* <span data-ttu-id="79596-348">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="79596-348">Custom lifetime management</span></span>
* <span data-ttu-id="79596-349">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="79596-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="79596-350">Registro baseado em convenções</span><span class="sxs-lookup"><span data-stu-id="79596-350">Convention-based registration</span></span>

<span data-ttu-id="79596-351">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="79596-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="79596-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="79596-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="79596-353">Dryioc</span><span class="sxs-lookup"><span data-stu-id="79596-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="79596-354">Grace</span><span class="sxs-lookup"><span data-stu-id="79596-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="79596-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="79596-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="79596-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="79596-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="79596-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="79596-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="79596-358">Unity</span><span class="sxs-lookup"><span data-stu-id="79596-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="79596-359">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="79596-359">Thread safety</span></span>

<span data-ttu-id="79596-360">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="79596-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="79596-361">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="79596-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="79596-362">O método de fábrica de serviço único, como o segundo argumento para [\<AddSingleton TService> (IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser seguro para threads.</span><span class="sxs-lookup"><span data-stu-id="79596-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="79596-363">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="79596-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="79596-364">Recomendações</span><span class="sxs-lookup"><span data-stu-id="79596-364">Recommendations</span></span>

* <span data-ttu-id="79596-365">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="79596-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="79596-366">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="79596-367">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="79596-368">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="79596-369">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="79596-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="79596-370">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="79596-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="79596-371">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="79596-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="79596-372">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="79596-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="79596-373">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="79596-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="79596-374">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="79596-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="79596-375">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="79596-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="79596-376">**Correto:**</span><span class="sxs-lookup"><span data-stu-id="79596-376">**Correct**:</span></span>

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

* <span data-ttu-id="79596-377">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="79596-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="79596-378">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="79596-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="79596-379">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="79596-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="79596-380">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="79596-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="79596-381">As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="79596-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="79596-382">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="79596-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="79596-383">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="79596-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79596-384">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="79596-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="79596-385">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="79596-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="79596-386">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="79596-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="79596-387">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="79596-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="79596-388">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79596-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79596-389">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="79596-390">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="79596-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="79596-391">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79596-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="79596-392">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="79596-392">Overview of dependency injection</span></span>

<span data-ttu-id="79596-393">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="79596-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="79596-394">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="79596-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="79596-395">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="79596-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="79596-396">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="79596-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="79596-397">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="79596-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="79596-398">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="79596-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="79596-399">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="79596-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="79596-400">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="79596-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="79596-401">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="79596-402">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="79596-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="79596-403">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="79596-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="79596-404">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="79596-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="79596-405">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="79596-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="79596-406">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="79596-407">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="79596-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="79596-408">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="79596-409">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="79596-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="79596-410">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="79596-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="79596-411">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="79596-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="79596-412">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="79596-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="79596-413">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="79596-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="79596-414">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="79596-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="79596-415">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="79596-416">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="79596-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="79596-417">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="79596-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="79596-418">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="79596-419">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="79596-420">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="79596-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="79596-421">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="79596-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="79596-422">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="79596-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="79596-423">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="79596-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="79596-424">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="79596-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="79596-425">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="79596-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="79596-426">Por exemplo, `services.AddMvc()` adiciona os serviços exigidos pelo MVC e por Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="79596-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="79596-427">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="79596-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="79596-428">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="79596-429">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="79596-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="79596-430">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="79596-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="79596-431">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="79596-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="79596-432">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="79596-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="79596-433">Serviços injetados em Startup</span><span class="sxs-lookup"><span data-stu-id="79596-433">Services injected into Startup</span></span>

<span data-ttu-id="79596-434">Somente os seguintes tipos de `Startup` serviço podem ser injetados<xref:Microsoft.Extensions.Hosting.IHostBuilder>no construtor ao usar o Host Genérico ( ):</span><span class="sxs-lookup"><span data-stu-id="79596-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="79596-435">Os serviços podem `Startup.Configure`ser injetados em:</span><span class="sxs-lookup"><span data-stu-id="79596-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="79596-436">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="79596-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="79596-437">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="79596-437">Framework-provided services</span></span>

<span data-ttu-id="79596-438">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos da plataforma, como Entity Framework Core e ASP.NET MVC Core.</span><span class="sxs-lookup"><span data-stu-id="79596-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="79596-439">Inicialmente, `IServiceCollection` o `ConfigureServices` fornecido tem serviços definidos pelo framework, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="79596-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="79596-440">Não é incomum que um aplicativo baseado em um modelo ASP.NET Core tenha centenas de serviços registrados pela framework.</span><span class="sxs-lookup"><span data-stu-id="79596-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="79596-441">Uma pequena amostra de serviços registrados em quadro está listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="79596-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="79596-442">Tipo de Serviço</span><span class="sxs-lookup"><span data-stu-id="79596-442">Service Type</span></span> | <span data-ttu-id="79596-443">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="79596-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="79596-444">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="79596-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="79596-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="79596-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="79596-448">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="79596-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="79596-450">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="79596-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="79596-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="79596-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="79596-454">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="79596-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="79596-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="79596-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="79596-458">Registre serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="79596-458">Register additional services with extension methods</span></span>

<span data-ttu-id="79596-459">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="79596-460">O código a seguir é um exemplo de como adicionar serviços adicionais ao <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>contêiner usando os métodos de extensão [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e :</span><span class="sxs-lookup"><span data-stu-id="79596-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="79596-461">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="79596-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="79596-462">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="79596-462">Service lifetimes</span></span>

<span data-ttu-id="79596-463">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="79596-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="79596-464">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="79596-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="79596-465">Transitório</span><span class="sxs-lookup"><span data-stu-id="79596-465">Transient</span></span>

<span data-ttu-id="79596-466">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="79596-467">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="79596-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="79596-468">Com escopo</span><span class="sxs-lookup"><span data-stu-id="79596-468">Scoped</span></span>

<span data-ttu-id="79596-469">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="79596-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="79596-470">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="79596-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="79596-471">Não injete por meio de injeção de construtor porque isso força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="79596-471">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="79596-472">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="79596-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="79596-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="79596-473">Singleton</span></span>

<span data-ttu-id="79596-474">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="79596-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="79596-475">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="79596-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="79596-476">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="79596-477">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="79596-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="79596-478">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="79596-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="79596-479">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="79596-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="79596-480">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="79596-480">Service registration methods</span></span>

<span data-ttu-id="79596-481">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="79596-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="79596-482">Método</span><span class="sxs-lookup"><span data-stu-id="79596-482">Method</span></span> | <span data-ttu-id="79596-483">Automático</span><span class="sxs-lookup"><span data-stu-id="79596-483">Automatic</span></span><br><span data-ttu-id="79596-484">objeto</span><span class="sxs-lookup"><span data-stu-id="79596-484">object</span></span><br><span data-ttu-id="79596-485">descarte</span><span class="sxs-lookup"><span data-stu-id="79596-485">disposal</span></span> | <span data-ttu-id="79596-486">Vários</span><span class="sxs-lookup"><span data-stu-id="79596-486">Multiple</span></span><br><span data-ttu-id="79596-487">implementações</span><span class="sxs-lookup"><span data-stu-id="79596-487">implementations</span></span> | <span data-ttu-id="79596-488">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="79596-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="79596-489">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="79596-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="79596-490">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-490">Yes</span></span> | <span data-ttu-id="79596-491">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-491">Yes</span></span> | <span data-ttu-id="79596-492">Não</span><span class="sxs-lookup"><span data-stu-id="79596-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="79596-493">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="79596-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="79596-494">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-494">Yes</span></span> | <span data-ttu-id="79596-495">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-495">Yes</span></span> | <span data-ttu-id="79596-496">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="79596-497">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="79596-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="79596-498">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-498">Yes</span></span> | <span data-ttu-id="79596-499">Não</span><span class="sxs-lookup"><span data-stu-id="79596-499">No</span></span> | <span data-ttu-id="79596-500">Não</span><span class="sxs-lookup"><span data-stu-id="79596-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="79596-501">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="79596-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="79596-502">Não</span><span class="sxs-lookup"><span data-stu-id="79596-502">No</span></span> | <span data-ttu-id="79596-503">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-503">Yes</span></span> | <span data-ttu-id="79596-504">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="79596-505">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="79596-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="79596-506">Não</span><span class="sxs-lookup"><span data-stu-id="79596-506">No</span></span> | <span data-ttu-id="79596-507">Não</span><span class="sxs-lookup"><span data-stu-id="79596-507">No</span></span> | <span data-ttu-id="79596-508">Sim</span><span class="sxs-lookup"><span data-stu-id="79596-508">Yes</span></span> |

<span data-ttu-id="79596-509">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="79596-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="79596-510">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="79596-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="79596-511">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="79596-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="79596-512">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="79596-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="79596-513">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="79596-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="79596-514">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="79596-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="79596-515">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="79596-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="79596-516">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="79596-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="79596-517">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="79596-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="79596-518">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="79596-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="79596-519">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="79596-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="79596-520">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="79596-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="79596-521">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="79596-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="79596-522">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="79596-522">Constructor injection behavior</span></span>

<span data-ttu-id="79596-523">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="79596-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="79596-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Permite a criação de objetos sem registro de serviço no recipiente de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="79596-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="79596-525">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="79596-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="79596-526">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="79596-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="79596-527">Quando os serviços são resolvidos por `IServiceProvider` ou `ActivatorUtilities`, a injeção do construtor exige um construtor *público*.</span><span class="sxs-lookup"><span data-stu-id="79596-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="79596-528">Quando os serviços são resolvidos por `ActivatorUtilities`, a injeção de construtor exige a existência de apenas de um construtor aplicável.</span><span class="sxs-lookup"><span data-stu-id="79596-528">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="79596-529">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="79596-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="79596-530">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="79596-530">Entity Framework contexts</span></span>

<span data-ttu-id="79596-531">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="79596-532">O tempo de vida padrão terá escopo se um tempo de vida não for especificado por uma sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="79596-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="79596-533">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="79596-534">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="79596-534">Lifetime and registration options</span></span>

<span data-ttu-id="79596-535">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="79596-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="79596-536">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="79596-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="79596-537">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="79596-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="79596-538">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="79596-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="79596-539">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="79596-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="79596-540">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="79596-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="79596-541">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="79596-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="79596-542">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="79596-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="79596-543">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="79596-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="79596-544">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="79596-545">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="79596-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="79596-546">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="79596-547">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="79596-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="79596-548">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="79596-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="79596-549">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="79596-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="79596-550">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="79596-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="79596-551">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="79596-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="79596-552">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="79596-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="79596-553">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="79596-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="79596-554">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="79596-554">**First request:**</span></span>

<span data-ttu-id="79596-555">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="79596-555">Controller operations:</span></span>

<span data-ttu-id="79596-556">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="79596-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="79596-557">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="79596-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="79596-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-559">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-560">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="79596-560">`OperationService` operations:</span></span>

<span data-ttu-id="79596-561">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="79596-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="79596-562">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="79596-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="79596-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-564">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-565">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="79596-565">**Second request:**</span></span>

<span data-ttu-id="79596-566">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="79596-566">Controller operations:</span></span>

<span data-ttu-id="79596-567">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="79596-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="79596-568">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="79596-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="79596-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-570">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-571">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="79596-571">`OperationService` operations:</span></span>

<span data-ttu-id="79596-572">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="79596-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="79596-573">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="79596-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="79596-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="79596-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="79596-575">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="79596-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="79596-576">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="79596-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="79596-577">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="79596-577">*Transient* objects are always different.</span></span> <span data-ttu-id="79596-578">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="79596-579">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="79596-580">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="79596-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="79596-581">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="79596-582">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="79596-582">Call services from main</span></span>

<span data-ttu-id="79596-583">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="79596-584">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="79596-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="79596-585">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="79596-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="79596-586">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="79596-586">Scope validation</span></span>

<span data-ttu-id="79596-587">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="79596-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="79596-588">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="79596-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="79596-589">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="79596-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="79596-590">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="79596-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="79596-591">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="79596-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="79596-592">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="79596-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="79596-593">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="79596-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="79596-594">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="79596-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="79596-595">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="79596-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="79596-596">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="79596-596">Request Services</span></span>

<span data-ttu-id="79596-597">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="79596-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="79596-598">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79596-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="79596-599">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="79596-600">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="79596-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="79596-601">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="79596-602">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="79596-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="79596-603">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="79596-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="79596-604">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="79596-604">Design services for dependency injection</span></span>

<span data-ttu-id="79596-605">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="79596-605">Best practices are to:</span></span>

* <span data-ttu-id="79596-606">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="79596-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="79596-607">Evite classes e membros estaduais e estáticos.</span><span class="sxs-lookup"><span data-stu-id="79596-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="79596-608">Designe aplicativos para usar serviços de singleton, que evitam criar estado global.</span><span class="sxs-lookup"><span data-stu-id="79596-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="79596-609">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="79596-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="79596-610">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="79596-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="79596-611">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="79596-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="79596-612">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="79596-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="79596-613">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="79596-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="79596-614">Lembre-se de que as classes de modelo de página de Razor Pages e as classes do controlador de MVC devem se concentrar em questões de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="79596-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="79596-615">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="79596-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="79596-616">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="79596-616">Disposal of services</span></span>

<span data-ttu-id="79596-617">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="79596-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="79596-618">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="79596-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="79596-619">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="79596-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="79596-620">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="79596-620">In the following example:</span></span>

* <span data-ttu-id="79596-621">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="79596-622">As vidas de serviço pretendidas não são conhecidas pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="79596-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="79596-623">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="79596-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="79596-624">Se os serviços não estiverem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="79596-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="79596-625">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="79596-625">Default service container replacement</span></span>

<span data-ttu-id="79596-626">O contêiner de serviço embutido foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumo.</span><span class="sxs-lookup"><span data-stu-id="79596-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="79596-627">Recomendamos o uso do contêiner embutido, a menos que você precise de um recurso específico que o contêiner embutido não suporte, tais como:</span><span class="sxs-lookup"><span data-stu-id="79596-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="79596-628">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="79596-628">Property injection</span></span>
* <span data-ttu-id="79596-629">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="79596-629">Injection based on name</span></span>
* <span data-ttu-id="79596-630">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="79596-630">Child containers</span></span>
* <span data-ttu-id="79596-631">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="79596-631">Custom lifetime management</span></span>
* <span data-ttu-id="79596-632">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="79596-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="79596-633">Registro baseado em convenções</span><span class="sxs-lookup"><span data-stu-id="79596-633">Convention-based registration</span></span>

<span data-ttu-id="79596-634">Os seguintes contêineres de terceiros podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="79596-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="79596-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="79596-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="79596-636">Dryioc</span><span class="sxs-lookup"><span data-stu-id="79596-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="79596-637">Grace</span><span class="sxs-lookup"><span data-stu-id="79596-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="79596-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="79596-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="79596-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="79596-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="79596-640">Stashbox</span><span class="sxs-lookup"><span data-stu-id="79596-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="79596-641">Unity</span><span class="sxs-lookup"><span data-stu-id="79596-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="79596-642">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="79596-642">Thread safety</span></span>

<span data-ttu-id="79596-643">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="79596-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="79596-644">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="79596-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="79596-645">O método de fábrica de serviço único, como o segundo argumento para [\<AddSingleton TService> (IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser seguro para threads.</span><span class="sxs-lookup"><span data-stu-id="79596-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="79596-646">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="79596-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="79596-647">Recomendações</span><span class="sxs-lookup"><span data-stu-id="79596-647">Recommendations</span></span>

* <span data-ttu-id="79596-648">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="79596-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="79596-649">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="79596-650">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="79596-651">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="79596-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="79596-652">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="79596-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="79596-653">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="79596-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="79596-654">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="79596-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="79596-655">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="79596-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="79596-656">Evite usar o *padrão localizador de serviço,* que mistura inversão de estratégias [de controle.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)</span><span class="sxs-lookup"><span data-stu-id="79596-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="79596-657">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar DI em vez disso:</span><span class="sxs-lookup"><span data-stu-id="79596-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="79596-658">**Incorreto:**</span><span class="sxs-lookup"><span data-stu-id="79596-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="79596-659">**Correto:**</span><span class="sxs-lookup"><span data-stu-id="79596-659">**Correct**:</span></span>

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

  * <span data-ttu-id="79596-660">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="79596-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="79596-661">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="79596-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="79596-662">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="79596-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="79596-663">As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="79596-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="79596-664">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="79596-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="79596-665">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="79596-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79596-666">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="79596-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="79596-667">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="79596-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="79596-668">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="79596-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="79596-669">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="79596-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="79596-670">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79596-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
