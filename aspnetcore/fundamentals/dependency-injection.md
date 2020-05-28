---
<span data-ttu-id="565ea-101">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-102">'Blazor'</span></span>
- <span data-ttu-id="565ea-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-103">'Identity'</span></span>
- <span data-ttu-id="565ea-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-105">'Razor'</span></span>
- <span data-ttu-id="565ea-106">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="565ea-107">Injeção de dependência no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="565ea-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="565ea-108">Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="565ea-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="565ea-109">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="565ea-110">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="565ea-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="565ea-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="565ea-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="565ea-112">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="565ea-112">Overview of dependency injection</span></span>

<span data-ttu-id="565ea-113">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="565ea-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="565ea-114">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="565ea-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="565ea-115">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="565ea-116">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="565ea-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="565ea-117">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="565ea-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="565ea-118">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="565ea-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="565ea-119">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="565ea-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="565ea-120">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="565ea-121">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="565ea-122">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="565ea-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="565ea-123">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="565ea-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="565ea-124">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="565ea-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="565ea-125">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="565ea-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="565ea-126">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="565ea-127">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="565ea-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="565ea-128">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="565ea-129">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="565ea-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="565ea-130">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="565ea-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="565ea-131">No [exemplo de aplicativo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="565ea-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="565ea-132">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="565ea-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="565ea-133">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="565ea-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="565ea-134">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="565ea-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="565ea-135">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="565ea-136">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="565ea-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="565ea-137">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="565ea-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="565ea-138">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="565ea-139">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="565ea-140">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="565ea-141">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="565ea-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="565ea-142">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="565ea-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="565ea-143">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="565ea-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="565ea-144">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="565ea-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="565ea-145">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="565ea-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="565ea-146">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="565ea-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="565ea-147">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="565ea-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="565ea-148">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="565ea-149">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="565ea-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="565ea-150">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="565ea-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="565ea-151">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="565ea-152">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="565ea-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="565ea-153">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="565ea-153">Services injected into Startup</span></span>

<span data-ttu-id="565ea-154">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="565ea-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="565ea-155">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="565ea-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="565ea-156">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="565ea-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="565ea-157">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="565ea-157">Framework-provided services</span></span>

<span data-ttu-id="565ea-158">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="565ea-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="565ea-159">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="565ea-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="565ea-160">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="565ea-161">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="565ea-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="565ea-162">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="565ea-162">Service Type</span></span> | <span data-ttu-id="565ea-163">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="565ea-163">Lifetime</span></span> |
| ---
<span data-ttu-id="565ea-164">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-165">'Blazor'</span></span>
- <span data-ttu-id="565ea-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-166">'Identity'</span></span>
- <span data-ttu-id="565ea-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-168">'Razor'</span></span>
- <span data-ttu-id="565ea-169">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-170">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-171">'Blazor'</span></span>
- <span data-ttu-id="565ea-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-172">'Identity'</span></span>
- <span data-ttu-id="565ea-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-174">'Razor'</span></span>
- <span data-ttu-id="565ea-175">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-176">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-177">'Blazor'</span></span>
- <span data-ttu-id="565ea-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-178">'Identity'</span></span>
- <span data-ttu-id="565ea-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-180">'Razor'</span></span>
- <span data-ttu-id="565ea-181">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-182">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-183">'Blazor'</span></span>
- <span data-ttu-id="565ea-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-184">'Identity'</span></span>
- <span data-ttu-id="565ea-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-186">'Razor'</span></span>
- <span data-ttu-id="565ea-187">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-187">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-188">------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-189">'Blazor'</span></span>
- <span data-ttu-id="565ea-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-190">'Identity'</span></span>
- <span data-ttu-id="565ea-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-192">'Razor'</span></span>
- <span data-ttu-id="565ea-193">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-194">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-195">'Blazor'</span></span>
- <span data-ttu-id="565ea-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-196">'Identity'</span></span>
- <span data-ttu-id="565ea-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-198">'Razor'</span></span>
- <span data-ttu-id="565ea-199">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-199">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitório | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitório | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="565ea-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="565ea-201">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="565ea-201">Register additional services with extension methods</span></span>

<span data-ttu-id="565ea-202">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="565ea-203">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="565ea-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="565ea-204">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="565ea-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="565ea-205">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="565ea-205">Service lifetimes</span></span>

<span data-ttu-id="565ea-206">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="565ea-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="565ea-207">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="565ea-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="565ea-208">Transitório</span><span class="sxs-lookup"><span data-stu-id="565ea-208">Transient</span></span>

<span data-ttu-id="565ea-209">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="565ea-210">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="565ea-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="565ea-211">Com escopo</span><span class="sxs-lookup"><span data-stu-id="565ea-211">Scoped</span></span>

<span data-ttu-id="565ea-212">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="565ea-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="565ea-213">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="565ea-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="565ea-214">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="565ea-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="565ea-215">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="565ea-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="565ea-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="565ea-216">Singleton</span></span>

<span data-ttu-id="565ea-217">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="565ea-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="565ea-218">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="565ea-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="565ea-219">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="565ea-220">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="565ea-221">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="565ea-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="565ea-222">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="565ea-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="565ea-223">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="565ea-223">Service registration methods</span></span>

<span data-ttu-id="565ea-224">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="565ea-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="565ea-225">Método</span><span class="sxs-lookup"><span data-stu-id="565ea-225">Method</span></span> | <span data-ttu-id="565ea-226">Automática</span><span class="sxs-lookup"><span data-stu-id="565ea-226">Automatic</span></span><br><span data-ttu-id="565ea-227">objeto</span><span class="sxs-lookup"><span data-stu-id="565ea-227">object</span></span><br><span data-ttu-id="565ea-228">descarte</span><span class="sxs-lookup"><span data-stu-id="565ea-228">disposal</span></span> | <span data-ttu-id="565ea-229">Vários</span><span class="sxs-lookup"><span data-stu-id="565ea-229">Multiple</span></span><br><span data-ttu-id="565ea-230">implementações</span><span class="sxs-lookup"><span data-stu-id="565ea-230">implementations</span></span> | <span data-ttu-id="565ea-231">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="565ea-231">Pass args</span></span> |
| ---
<span data-ttu-id="565ea-232">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-233">'Blazor'</span></span>
- <span data-ttu-id="565ea-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-234">'Identity'</span></span>
- <span data-ttu-id="565ea-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-236">'Razor'</span></span>
- <span data-ttu-id="565ea-237">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-237">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-238">--- | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-239">'Blazor'</span></span>
- <span data-ttu-id="565ea-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-240">'Identity'</span></span>
- <span data-ttu-id="565ea-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-242">'Razor'</span></span>
- <span data-ttu-id="565ea-243">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-244">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-245">'Blazor'</span></span>
- <span data-ttu-id="565ea-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-246">'Identity'</span></span>
- <span data-ttu-id="565ea-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-248">'Razor'</span></span>
- <span data-ttu-id="565ea-249">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-250">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-251">'Blazor'</span></span>
- <span data-ttu-id="565ea-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-252">'Identity'</span></span>
- <span data-ttu-id="565ea-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-254">'Razor'</span></span>
- <span data-ttu-id="565ea-255">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-256">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-257">'Blazor'</span></span>
- <span data-ttu-id="565ea-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-258">'Identity'</span></span>
- <span data-ttu-id="565ea-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-260">'Razor'</span></span>
- <span data-ttu-id="565ea-261">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-262">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-263">'Blazor'</span></span>
- <span data-ttu-id="565ea-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-264">'Identity'</span></span>
- <span data-ttu-id="565ea-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-266">'Razor'</span></span>
- <span data-ttu-id="565ea-267">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-268">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-269">'Blazor'</span></span>
- <span data-ttu-id="565ea-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-270">'Identity'</span></span>
- <span data-ttu-id="565ea-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-272">'Razor'</span></span>
- <span data-ttu-id="565ea-273">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-274">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-275">'Blazor'</span></span>
- <span data-ttu-id="565ea-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-276">'Identity'</span></span>
- <span data-ttu-id="565ea-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-278">'Razor'</span></span>
- <span data-ttu-id="565ea-279">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-280">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-281">'Blazor'</span></span>
- <span data-ttu-id="565ea-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-282">'Identity'</span></span>
- <span data-ttu-id="565ea-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-284">'Razor'</span></span>
- <span data-ttu-id="565ea-285">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-286">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-287">'Blazor'</span></span>
- <span data-ttu-id="565ea-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-288">'Identity'</span></span>
- <span data-ttu-id="565ea-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-290">'Razor'</span></span>
- <span data-ttu-id="565ea-291">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-292">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-293">'Blazor'</span></span>
- <span data-ttu-id="565ea-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-294">'Identity'</span></span>
- <span data-ttu-id="565ea-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-296">'Razor'</span></span>
- <span data-ttu-id="565ea-297">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-298">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-299">'Blazor'</span></span>
- <span data-ttu-id="565ea-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-300">'Identity'</span></span>
- <span data-ttu-id="565ea-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-302">'Razor'</span></span>
- <span data-ttu-id="565ea-303">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-304">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-305">'Blazor'</span></span>
- <span data-ttu-id="565ea-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-306">'Identity'</span></span>
- <span data-ttu-id="565ea-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-308">'Razor'</span></span>
- <span data-ttu-id="565ea-309">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-309">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-310">---------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-311">'Blazor'</span></span>
- <span data-ttu-id="565ea-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-312">'Identity'</span></span>
- <span data-ttu-id="565ea-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-314">'Razor'</span></span>
- <span data-ttu-id="565ea-315">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-316">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-317">'Blazor'</span></span>
- <span data-ttu-id="565ea-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-318">'Identity'</span></span>
- <span data-ttu-id="565ea-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-320">'Razor'</span></span>
- <span data-ttu-id="565ea-321">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-322">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-323">'Blazor'</span></span>
- <span data-ttu-id="565ea-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-324">'Identity'</span></span>
- <span data-ttu-id="565ea-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-326">'Razor'</span></span>
- <span data-ttu-id="565ea-327">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-328">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-329">'Blazor'</span></span>
- <span data-ttu-id="565ea-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-330">'Identity'</span></span>
- <span data-ttu-id="565ea-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-332">'Razor'</span></span>
- <span data-ttu-id="565ea-333">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-334">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-335">'Blazor'</span></span>
- <span data-ttu-id="565ea-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-336">'Identity'</span></span>
- <span data-ttu-id="565ea-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-338">'Razor'</span></span>
- <span data-ttu-id="565ea-339">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-340">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-341">'Blazor'</span></span>
- <span data-ttu-id="565ea-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-342">'Identity'</span></span>
- <span data-ttu-id="565ea-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-344">'Razor'</span></span>
- <span data-ttu-id="565ea-345">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-346">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-347">'Blazor'</span></span>
- <span data-ttu-id="565ea-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-348">'Identity'</span></span>
- <span data-ttu-id="565ea-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-350">'Razor'</span></span>
- <span data-ttu-id="565ea-351">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-352">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-353">'Blazor'</span></span>
- <span data-ttu-id="565ea-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-354">'Identity'</span></span>
- <span data-ttu-id="565ea-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-356">'Razor'</span></span>
- <span data-ttu-id="565ea-357">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-358">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-359">'Blazor'</span></span>
- <span data-ttu-id="565ea-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-360">'Identity'</span></span>
- <span data-ttu-id="565ea-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-362">'Razor'</span></span>
- <span data-ttu-id="565ea-363">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-364">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-365">'Blazor'</span></span>
- <span data-ttu-id="565ea-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-366">'Identity'</span></span>
- <span data-ttu-id="565ea-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-368">'Razor'</span></span>
- <span data-ttu-id="565ea-369">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-369">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-370">-------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-371">'Blazor'</span></span>
- <span data-ttu-id="565ea-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-372">'Identity'</span></span>
- <span data-ttu-id="565ea-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-374">'Razor'</span></span>
- <span data-ttu-id="565ea-375">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-375">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="565ea-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="565ea-377">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="565ea-377">Example:</span></span><br><span data-ttu-id="565ea-378">`services.AddSingleton<IMyDep, MyDep>();`| Sim | Sim | Não | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="565ea-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="565ea-379">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="565ea-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="565ea-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Sim | Sim | Sim | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="565ea-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="565ea-381">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="565ea-381">Example:</span></span><br><span data-ttu-id="565ea-382">`services.AddSingleton<MyDep>();`| Sim | Não | Não | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="565ea-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="565ea-383">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="565ea-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="565ea-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Não | Sim | Sim | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="565ea-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="565ea-385">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="565ea-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="565ea-386">`services.AddSingleton(new MyDep("A string!"));`| Não | Não | Sim |</span><span class="sxs-lookup"><span data-stu-id="565ea-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="565ea-387">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="565ea-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="565ea-388">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="565ea-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="565ea-389">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="565ea-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="565ea-390">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="565ea-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="565ea-391">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="565ea-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="565ea-392">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="565ea-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="565ea-393">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="565ea-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="565ea-394">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="565ea-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="565ea-395">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="565ea-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="565ea-396">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="565ea-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="565ea-397">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="565ea-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="565ea-398">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="565ea-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="565ea-399">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="565ea-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="565ea-400">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="565ea-400">Constructor injection behavior</span></span>

<span data-ttu-id="565ea-401">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="565ea-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="565ea-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="565ea-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="565ea-403">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="565ea-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="565ea-404">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="565ea-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="565ea-405">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="565ea-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="565ea-406">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="565ea-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="565ea-407">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="565ea-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="565ea-408">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="565ea-408">Entity Framework contexts</span></span>

<span data-ttu-id="565ea-409">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="565ea-410">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="565ea-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="565ea-411">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="565ea-412">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="565ea-412">Lifetime and registration options</span></span>

<span data-ttu-id="565ea-413">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="565ea-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="565ea-414">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="565ea-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="565ea-415">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="565ea-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="565ea-416">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="565ea-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="565ea-417">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="565ea-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="565ea-418">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="565ea-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="565ea-419">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="565ea-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="565ea-420">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="565ea-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="565ea-421">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="565ea-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="565ea-422">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="565ea-423">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="565ea-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="565ea-424">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="565ea-425">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="565ea-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="565ea-426">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="565ea-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="565ea-427">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="565ea-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="565ea-428">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="565ea-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="565ea-429">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="565ea-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="565ea-430">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="565ea-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="565ea-431">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="565ea-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="565ea-432">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="565ea-432">**First request:**</span></span>

<span data-ttu-id="565ea-433">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="565ea-433">Controller operations:</span></span>

<span data-ttu-id="565ea-434">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="565ea-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="565ea-435">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="565ea-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="565ea-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-437">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-438">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="565ea-438">`OperationService` operations:</span></span>

<span data-ttu-id="565ea-439">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="565ea-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="565ea-440">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="565ea-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="565ea-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-442">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-443">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="565ea-443">**Second request:**</span></span>

<span data-ttu-id="565ea-444">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="565ea-444">Controller operations:</span></span>

<span data-ttu-id="565ea-445">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="565ea-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="565ea-446">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="565ea-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="565ea-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-448">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-449">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="565ea-449">`OperationService` operations:</span></span>

<span data-ttu-id="565ea-450">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="565ea-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="565ea-451">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="565ea-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="565ea-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-453">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-454">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="565ea-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="565ea-455">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="565ea-455">*Transient* objects are always different.</span></span> <span data-ttu-id="565ea-456">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="565ea-457">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="565ea-458">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="565ea-459">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="565ea-460">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="565ea-460">Call services from main</span></span>

<span data-ttu-id="565ea-461">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="565ea-462">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="565ea-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="565ea-463">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="565ea-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="565ea-464">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="565ea-464">Scope validation</span></span>

<span data-ttu-id="565ea-465">Quando o aplicativo está em execução no ambiente de desenvolvimento e chama [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para criar o host, o provedor de serviço padrão executa verificações para verificar se:</span><span class="sxs-lookup"><span data-stu-id="565ea-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="565ea-466">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="565ea-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="565ea-467">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="565ea-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="565ea-468">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="565ea-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="565ea-469">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="565ea-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="565ea-470">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="565ea-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="565ea-471">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="565ea-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="565ea-472">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="565ea-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="565ea-473">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="565ea-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="565ea-474">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="565ea-474">Request Services</span></span>

<span data-ttu-id="565ea-475">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="565ea-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="565ea-476">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="565ea-477">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="565ea-478">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="565ea-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="565ea-479">Em vez disso, solicite os tipos que as classes exigem por meio de construtores de classe e permita que a estrutura Insira as dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="565ea-480">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="565ea-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="565ea-481">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="565ea-482">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="565ea-482">Design services for dependency injection</span></span>

<span data-ttu-id="565ea-483">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="565ea-483">Best practices are to:</span></span>

* <span data-ttu-id="565ea-484">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="565ea-485">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="565ea-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="565ea-486">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="565ea-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="565ea-487">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="565ea-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="565ea-488">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="565ea-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="565ea-489">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="565ea-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="565ea-490">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="565ea-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="565ea-491">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="565ea-492">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="565ea-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="565ea-493">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="565ea-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="565ea-494">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="565ea-494">Disposal of services</span></span>

<span data-ttu-id="565ea-495">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="565ea-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="565ea-496">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="565ea-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="565ea-497">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="565ea-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="565ea-498">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="565ea-498">In the following example:</span></span>

* <span data-ttu-id="565ea-499">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="565ea-500">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="565ea-501">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="565ea-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="565ea-502">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="565ea-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="565ea-503">Para obter uma discussão sobre as opções de descarte de serviço, consulte [quatro maneiras de descartar idisposables em ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="565ea-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="565ea-504">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="565ea-504">Default service container replacement</span></span>

<span data-ttu-id="565ea-505">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="565ea-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="565ea-506">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="565ea-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="565ea-507">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="565ea-507">Property injection</span></span>
* <span data-ttu-id="565ea-508">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="565ea-508">Injection based on name</span></span>
* <span data-ttu-id="565ea-509">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="565ea-509">Child containers</span></span>
* <span data-ttu-id="565ea-510">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="565ea-510">Custom lifetime management</span></span>
* <span data-ttu-id="565ea-511">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="565ea-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="565ea-512">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="565ea-512">Convention-based registration</span></span>

<span data-ttu-id="565ea-513">Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="565ea-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="565ea-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="565ea-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="565ea-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="565ea-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="565ea-516">Grace</span><span class="sxs-lookup"><span data-stu-id="565ea-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="565ea-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="565ea-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="565ea-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="565ea-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="565ea-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="565ea-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="565ea-520">Unity</span><span class="sxs-lookup"><span data-stu-id="565ea-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="565ea-521">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="565ea-521">Thread safety</span></span>

<span data-ttu-id="565ea-522">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="565ea-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="565ea-523">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="565ea-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="565ea-524">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="565ea-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="565ea-525">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="565ea-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="565ea-526">Recomendações</span><span class="sxs-lookup"><span data-stu-id="565ea-526">Recommendations</span></span>

* <span data-ttu-id="565ea-527">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="565ea-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="565ea-528">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="565ea-529">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="565ea-530">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="565ea-531">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="565ea-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="565ea-532">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="565ea-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="565ea-533">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="565ea-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="565ea-534">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="565ea-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="565ea-535">Evite usar o *padrão do localizador de serviço*.</span><span class="sxs-lookup"><span data-stu-id="565ea-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="565ea-536">Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:</span><span class="sxs-lookup"><span data-stu-id="565ea-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="565ea-537">**Incorreto**</span><span class="sxs-lookup"><span data-stu-id="565ea-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="565ea-538">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="565ea-538">**Correct**:</span></span>

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

* <span data-ttu-id="565ea-539">Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em runtime.</span><span class="sxs-lookup"><span data-stu-id="565ea-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="565ea-540">Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="565ea-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="565ea-541">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="565ea-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="565ea-542">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="565ea-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="565ea-543">As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="565ea-544">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="565ea-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="565ea-545">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="565ea-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="565ea-546">Padrões recomendados para multilocação em DI</span><span class="sxs-lookup"><span data-stu-id="565ea-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="565ea-547">O [Orchard Core](https://github.com/OrchardCMS/OrchardCore) fornece multilocação.</span><span class="sxs-lookup"><span data-stu-id="565ea-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="565ea-548">Para obter mais informações, consulte a [documentação do Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="565ea-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="565ea-549">Consulte os aplicativos de exemplos em https://github.com/OrchardCMS/OrchardCore.Samples para obter exemplos de como criar aplicativos modulares e multilocatários usando o Orchard Core Framework sem nenhum dos recursos específicos do CMS.</span><span class="sxs-lookup"><span data-stu-id="565ea-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="565ea-550">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="565ea-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="565ea-551">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="565ea-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="565ea-552">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="565ea-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="565ea-553">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="565ea-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="565ea-554">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="565ea-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="565ea-555">O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="565ea-556">Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="565ea-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="565ea-557">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="565ea-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="565ea-558">Visão geral da injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="565ea-558">Overview of dependency injection</span></span>

<span data-ttu-id="565ea-559">Uma *dependência* é qualquer objeto exigido por outro objeto.</span><span class="sxs-lookup"><span data-stu-id="565ea-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="565ea-560">Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:</span><span class="sxs-lookup"><span data-stu-id="565ea-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="565ea-561">Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="565ea-562">A classe  é uma dependência da classe:</span><span class="sxs-lookup"><span data-stu-id="565ea-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="565ea-563">A classe cria e depende diretamente da instância `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="565ea-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="565ea-564">As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:</span><span class="sxs-lookup"><span data-stu-id="565ea-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="565ea-565">Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.</span><span class="sxs-lookup"><span data-stu-id="565ea-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="565ea-566">Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="565ea-567">Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="565ea-568">É difícil testar a unidade dessa implementação.</span><span class="sxs-lookup"><span data-stu-id="565ea-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="565ea-569">O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="565ea-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="565ea-570">Injeção de dependência trata desses problemas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="565ea-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="565ea-571">O uso de uma interface ou classe base para abstrair a implementação da dependência.</span><span class="sxs-lookup"><span data-stu-id="565ea-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="565ea-572">Registrando a dependência em um contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="565ea-573">O ASP.NET Core fornece um contêiner de serviço interno, o <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="565ea-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="565ea-574">Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="565ea-575">*Injeção* do serviço no construtor da classe na qual ele é usado.</span><span class="sxs-lookup"><span data-stu-id="565ea-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="565ea-576">A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.</span><span class="sxs-lookup"><span data-stu-id="565ea-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="565ea-577">No [exemplo de aplicativo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:</span><span class="sxs-lookup"><span data-stu-id="565ea-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="565ea-578">Essa interface é implementada por um tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="565ea-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="565ea-579">`MyDependency` solicita um <xref:Microsoft.Extensions.Logging.ILogger`1> em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="565ea-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="565ea-580">Não é incomum usar a injeção de dependência de uma maneira encadeada.</span><span class="sxs-lookup"><span data-stu-id="565ea-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="565ea-581">Por sua vez, cada dependência solicitada solicita suas próprias dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="565ea-582">O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido.</span><span class="sxs-lookup"><span data-stu-id="565ea-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="565ea-583">O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.</span><span class="sxs-lookup"><span data-stu-id="565ea-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="565ea-584">`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="565ea-585">`IMyDependency` está registrado em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="565ea-586">`ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log, portanto, é um [serviço fornecido por estrutura](#framework-provided-services) registrado por padrão pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="565ea-587">O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="565ea-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="565ea-588">No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="565ea-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="565ea-589">O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação.</span><span class="sxs-lookup"><span data-stu-id="565ea-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="565ea-590">Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="565ea-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="565ea-591">Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços.</span><span class="sxs-lookup"><span data-stu-id="565ea-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="565ea-592">Por exemplo, `services.AddMvc()` adiciona as páginas de serviços Razor e a MVC necessária.</span><span class="sxs-lookup"><span data-stu-id="565ea-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="565ea-593">Recomendamos que os aplicativos sigam essa convenção.</span><span class="sxs-lookup"><span data-stu-id="565ea-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="565ea-594">Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="565ea-595">Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="565ea-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="565ea-596">Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular.</span><span class="sxs-lookup"><span data-stu-id="565ea-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="565ea-597">O campo é usado para acessar o serviço conforme o necessário na classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="565ea-598">No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:</span><span class="sxs-lookup"><span data-stu-id="565ea-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="565ea-599">Serviços injetados na inicialização</span><span class="sxs-lookup"><span data-stu-id="565ea-599">Services injected into Startup</span></span>

<span data-ttu-id="565ea-600">Somente os seguintes tipos de serviço podem ser injetados no `Startup` Construtor ao usar o host genérico ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ):</span><span class="sxs-lookup"><span data-stu-id="565ea-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="565ea-601">Os serviços podem ser injetados em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="565ea-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="565ea-602">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="565ea-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="565ea-603">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="565ea-603">Framework-provided services</span></span>

<span data-ttu-id="565ea-604">O `Startup.ConfigureServices` método é responsável por definir os serviços que o aplicativo usa, incluindo recursos de plataforma, como Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="565ea-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="565ea-605">Inicialmente, o `IServiceCollection` fornecido para o `ConfigureServices` tem serviços definidos pela estrutura, dependendo de [como o host foi configurado](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="565ea-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="565ea-606">Não é incomum um aplicativo baseado em um modelo de ASP.NET Core ter centenas de serviços registrados pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="565ea-607">Uma pequena amostra de serviços registrados na estrutura é listada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="565ea-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="565ea-608">Tipo de serviço</span><span class="sxs-lookup"><span data-stu-id="565ea-608">Service Type</span></span> | <span data-ttu-id="565ea-609">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="565ea-609">Lifetime</span></span> |
| ---
<span data-ttu-id="565ea-610">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-611">'Blazor'</span></span>
- <span data-ttu-id="565ea-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-612">'Identity'</span></span>
- <span data-ttu-id="565ea-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-614">'Razor'</span></span>
- <span data-ttu-id="565ea-615">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-616">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-617">'Blazor'</span></span>
- <span data-ttu-id="565ea-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-618">'Identity'</span></span>
- <span data-ttu-id="565ea-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-620">'Razor'</span></span>
- <span data-ttu-id="565ea-621">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-622">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-623">'Blazor'</span></span>
- <span data-ttu-id="565ea-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-624">'Identity'</span></span>
- <span data-ttu-id="565ea-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-626">'Razor'</span></span>
- <span data-ttu-id="565ea-627">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-628">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-629">'Blazor'</span></span>
- <span data-ttu-id="565ea-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-630">'Identity'</span></span>
- <span data-ttu-id="565ea-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-632">'Razor'</span></span>
- <span data-ttu-id="565ea-633">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-633">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-634">------ | título do---: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-635">'Blazor'</span></span>
- <span data-ttu-id="565ea-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-636">'Identity'</span></span>
- <span data-ttu-id="565ea-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-638">'Razor'</span></span>
- <span data-ttu-id="565ea-639">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-640">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-641">'Blazor'</span></span>
- <span data-ttu-id="565ea-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-642">'Identity'</span></span>
- <span data-ttu-id="565ea-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-644">'Razor'</span></span>
- <span data-ttu-id="565ea-645">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-645">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitório | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitório | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitório | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="565ea-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="565ea-647">Registrar serviços adicionais com métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="565ea-647">Register additional services with extension methods</span></span>

<span data-ttu-id="565ea-648">Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="565ea-649">O código a seguir é um exemplo de como adicionar serviços adicionais ao contêiner usando os métodos de extensão [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) e <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="565ea-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="565ea-650">Para saber mais, confira a classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> na documentação da API.</span><span class="sxs-lookup"><span data-stu-id="565ea-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="565ea-651">Tempos de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="565ea-651">Service lifetimes</span></span>

<span data-ttu-id="565ea-652">Escolha um tempo de vida apropriado para cada serviço registrado.</span><span class="sxs-lookup"><span data-stu-id="565ea-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="565ea-653">Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:</span><span class="sxs-lookup"><span data-stu-id="565ea-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="565ea-654">Transitório</span><span class="sxs-lookup"><span data-stu-id="565ea-654">Transient</span></span>

<span data-ttu-id="565ea-655">Serviços temporários de tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) são criados cada vez que são solicitados pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="565ea-656">Esse tempo de vida funciona melhor para serviços leves e sem estado.</span><span class="sxs-lookup"><span data-stu-id="565ea-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="565ea-657">Com escopo</span><span class="sxs-lookup"><span data-stu-id="565ea-657">Scoped</span></span>

<span data-ttu-id="565ea-658">Os serviços com tempo de vida (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) com escopo são criados uma vez por solicitação de cliente (conexão).</span><span class="sxs-lookup"><span data-stu-id="565ea-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="565ea-659">Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="565ea-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="565ea-660">Não injetar via [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) porque força o serviço a se comportar como um singleton.</span><span class="sxs-lookup"><span data-stu-id="565ea-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="565ea-661">Para obter mais informações, consulte <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="565ea-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="565ea-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="565ea-662">Singleton</span></span>

<span data-ttu-id="565ea-663">Serviços de tempo de vida singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) são criados na primeira solicitação (ou quando `Startup.ConfigureServices` é executado e uma instância é especificada com o registro do serviço).</span><span class="sxs-lookup"><span data-stu-id="565ea-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="565ea-664">Cada solicitação subsequente usa a mesma instância.</span><span class="sxs-lookup"><span data-stu-id="565ea-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="565ea-665">Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="565ea-666">Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="565ea-667">É perigoso resolver um serviço com escopo de um singleton.</span><span class="sxs-lookup"><span data-stu-id="565ea-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="565ea-668">Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.</span><span class="sxs-lookup"><span data-stu-id="565ea-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="565ea-669">Métodos de registro do serviço</span><span class="sxs-lookup"><span data-stu-id="565ea-669">Service registration methods</span></span>

<span data-ttu-id="565ea-670">Os métodos de extensão de registro de serviço oferecem sobrecargas que são úteis em cenários específicos.</span><span class="sxs-lookup"><span data-stu-id="565ea-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="565ea-671">Método</span><span class="sxs-lookup"><span data-stu-id="565ea-671">Method</span></span> | <span data-ttu-id="565ea-672">Automática</span><span class="sxs-lookup"><span data-stu-id="565ea-672">Automatic</span></span><br><span data-ttu-id="565ea-673">objeto</span><span class="sxs-lookup"><span data-stu-id="565ea-673">object</span></span><br><span data-ttu-id="565ea-674">descarte</span><span class="sxs-lookup"><span data-stu-id="565ea-674">disposal</span></span> | <span data-ttu-id="565ea-675">Vários</span><span class="sxs-lookup"><span data-stu-id="565ea-675">Multiple</span></span><br><span data-ttu-id="565ea-676">implementações</span><span class="sxs-lookup"><span data-stu-id="565ea-676">implementations</span></span> | <span data-ttu-id="565ea-677">Passar argumentos</span><span class="sxs-lookup"><span data-stu-id="565ea-677">Pass args</span></span> |
| ---
<span data-ttu-id="565ea-678">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-679">'Blazor'</span></span>
- <span data-ttu-id="565ea-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-680">'Identity'</span></span>
- <span data-ttu-id="565ea-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-682">'Razor'</span></span>
- <span data-ttu-id="565ea-683">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-683">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-684">--- | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-685">'Blazor'</span></span>
- <span data-ttu-id="565ea-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-686">'Identity'</span></span>
- <span data-ttu-id="565ea-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-688">'Razor'</span></span>
- <span data-ttu-id="565ea-689">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-690">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-691">'Blazor'</span></span>
- <span data-ttu-id="565ea-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-692">'Identity'</span></span>
- <span data-ttu-id="565ea-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-694">'Razor'</span></span>
- <span data-ttu-id="565ea-695">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-696">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-697">'Blazor'</span></span>
- <span data-ttu-id="565ea-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-698">'Identity'</span></span>
- <span data-ttu-id="565ea-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-700">'Razor'</span></span>
- <span data-ttu-id="565ea-701">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-702">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-703">'Blazor'</span></span>
- <span data-ttu-id="565ea-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-704">'Identity'</span></span>
- <span data-ttu-id="565ea-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-706">'Razor'</span></span>
- <span data-ttu-id="565ea-707">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-708">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-709">'Blazor'</span></span>
- <span data-ttu-id="565ea-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-710">'Identity'</span></span>
- <span data-ttu-id="565ea-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-712">'Razor'</span></span>
- <span data-ttu-id="565ea-713">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-714">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-715">'Blazor'</span></span>
- <span data-ttu-id="565ea-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-716">'Identity'</span></span>
- <span data-ttu-id="565ea-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-718">'Razor'</span></span>
- <span data-ttu-id="565ea-719">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-720">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-721">'Blazor'</span></span>
- <span data-ttu-id="565ea-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-722">'Identity'</span></span>
- <span data-ttu-id="565ea-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-724">'Razor'</span></span>
- <span data-ttu-id="565ea-725">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-726">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-727">'Blazor'</span></span>
- <span data-ttu-id="565ea-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-728">'Identity'</span></span>
- <span data-ttu-id="565ea-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-730">'Razor'</span></span>
- <span data-ttu-id="565ea-731">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-732">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-733">'Blazor'</span></span>
- <span data-ttu-id="565ea-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-734">'Identity'</span></span>
- <span data-ttu-id="565ea-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-736">'Razor'</span></span>
- <span data-ttu-id="565ea-737">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-738">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-739">'Blazor'</span></span>
- <span data-ttu-id="565ea-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-740">'Identity'</span></span>
- <span data-ttu-id="565ea-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-742">'Razor'</span></span>
- <span data-ttu-id="565ea-743">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-744">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-745">'Blazor'</span></span>
- <span data-ttu-id="565ea-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-746">'Identity'</span></span>
- <span data-ttu-id="565ea-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-748">'Razor'</span></span>
- <span data-ttu-id="565ea-749">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-750">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-751">'Blazor'</span></span>
- <span data-ttu-id="565ea-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-752">'Identity'</span></span>
- <span data-ttu-id="565ea-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-754">'Razor'</span></span>
- <span data-ttu-id="565ea-755">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-755">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-756">---------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-757">'Blazor'</span></span>
- <span data-ttu-id="565ea-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-758">'Identity'</span></span>
- <span data-ttu-id="565ea-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-760">'Razor'</span></span>
- <span data-ttu-id="565ea-761">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-762">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-763">'Blazor'</span></span>
- <span data-ttu-id="565ea-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-764">'Identity'</span></span>
- <span data-ttu-id="565ea-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-766">'Razor'</span></span>
- <span data-ttu-id="565ea-767">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-768">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-769">'Blazor'</span></span>
- <span data-ttu-id="565ea-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-770">'Identity'</span></span>
- <span data-ttu-id="565ea-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-772">'Razor'</span></span>
- <span data-ttu-id="565ea-773">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-774">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-775">'Blazor'</span></span>
- <span data-ttu-id="565ea-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-776">'Identity'</span></span>
- <span data-ttu-id="565ea-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-778">'Razor'</span></span>
- <span data-ttu-id="565ea-779">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-780">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-781">'Blazor'</span></span>
- <span data-ttu-id="565ea-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-782">'Identity'</span></span>
- <span data-ttu-id="565ea-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-784">'Razor'</span></span>
- <span data-ttu-id="565ea-785">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-786">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-787">'Blazor'</span></span>
- <span data-ttu-id="565ea-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-788">'Identity'</span></span>
- <span data-ttu-id="565ea-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-790">'Razor'</span></span>
- <span data-ttu-id="565ea-791">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-792">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-793">'Blazor'</span></span>
- <span data-ttu-id="565ea-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-794">'Identity'</span></span>
- <span data-ttu-id="565ea-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-796">'Razor'</span></span>
- <span data-ttu-id="565ea-797">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-798">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-799">'Blazor'</span></span>
- <span data-ttu-id="565ea-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-800">'Identity'</span></span>
- <span data-ttu-id="565ea-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-802">'Razor'</span></span>
- <span data-ttu-id="565ea-803">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-804">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-805">'Blazor'</span></span>
- <span data-ttu-id="565ea-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-806">'Identity'</span></span>
- <span data-ttu-id="565ea-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-808">'Razor'</span></span>
- <span data-ttu-id="565ea-809">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="565ea-810">Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-811">'Blazor'</span></span>
- <span data-ttu-id="565ea-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-812">'Identity'</span></span>
- <span data-ttu-id="565ea-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-814">'Razor'</span></span>
- <span data-ttu-id="565ea-815">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-815">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-816">-------------: | :---Título: autor: Descrição: monikerRange: MS. autor: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="565ea-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="565ea-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="565ea-817">'Blazor'</span></span>
- <span data-ttu-id="565ea-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="565ea-818">'Identity'</span></span>
- <span data-ttu-id="565ea-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="565ea-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="565ea-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="565ea-820">'Razor'</span></span>
- <span data-ttu-id="565ea-821">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="565ea-821">'SignalR' uid:</span></span> 

<span data-ttu-id="565ea-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="565ea-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="565ea-823">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="565ea-823">Example:</span></span><br><span data-ttu-id="565ea-824">`services.AddSingleton<IMyDep, MyDep>();`| Sim | Sim | Não | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="565ea-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="565ea-825">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="565ea-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="565ea-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Sim | Sim | Sim | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="565ea-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="565ea-827">Exemplo:</span><span class="sxs-lookup"><span data-stu-id="565ea-827">Example:</span></span><br><span data-ttu-id="565ea-828">`services.AddSingleton<MyDep>();`| Sim | Não | Não | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="565ea-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="565ea-829">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="565ea-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="565ea-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Não | Sim | Sim | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="565ea-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="565ea-831">Exemplos:</span><span class="sxs-lookup"><span data-stu-id="565ea-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="565ea-832">`services.AddSingleton(new MyDep("A string!"));`| Não | Não | Sim |</span><span class="sxs-lookup"><span data-stu-id="565ea-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="565ea-833">Para obter mais informações sobre o descarte de tipos, consulte a seção [Descarte de serviços](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="565ea-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="565ea-834">Um cenário comum para várias implementações é a [simulação de tipos para teste](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="565ea-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="565ea-835">Métodos `TryAdd{LIFETIME}` somente registrarão o serviço se ainda não houver uma implementação registrada.</span><span class="sxs-lookup"><span data-stu-id="565ea-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="565ea-836">No exemplo a seguir, a primeira linha registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="565ea-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="565ea-837">A segunda linha não tem nenhum efeito porque `IMyDependency` já tem uma implementação registrada:</span><span class="sxs-lookup"><span data-stu-id="565ea-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="565ea-838">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="565ea-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="565ea-839">Métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) apenas registrarão o serviço se ainda não houver uma implementação *do mesmo tipo*.</span><span class="sxs-lookup"><span data-stu-id="565ea-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="565ea-840">Vários serviços são resolvidos via `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="565ea-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="565ea-841">Ao registrar os serviços, só convém ao desenvolvedor adicionar uma instância se até o momento nenhuma do mesmo tipo foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="565ea-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="565ea-842">Em geral, esse método é usado por autores de biblioteca para evitar registrar duas cópias de uma instância no contêiner.</span><span class="sxs-lookup"><span data-stu-id="565ea-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="565ea-843">No exemplo a seguir, a primeira linha registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="565ea-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="565ea-844">A segunda linha registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="565ea-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="565ea-845">A terceira linha não tem nenhum efeito porque `IMyDep1` já tem uma implementação registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="565ea-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="565ea-846">Comportamento da injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="565ea-846">Constructor injection behavior</span></span>

<span data-ttu-id="565ea-847">Os serviços podem ser resolvidos por dois mecanismos:</span><span class="sxs-lookup"><span data-stu-id="565ea-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="565ea-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permite a criação de objeto sem registro de serviço no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="565ea-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="565ea-849">`ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.</span><span class="sxs-lookup"><span data-stu-id="565ea-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="565ea-850">Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.</span><span class="sxs-lookup"><span data-stu-id="565ea-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="565ea-851">Quando os serviços são resolvidos pelo `IServiceProvider` ou `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer um construtor *público* .</span><span class="sxs-lookup"><span data-stu-id="565ea-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="565ea-852">Quando os serviços são resolvidos pelo `ActivatorUtilities` , a [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) requer que apenas um Construtor aplicável exista.</span><span class="sxs-lookup"><span data-stu-id="565ea-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="565ea-853">Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="565ea-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="565ea-854">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="565ea-854">Entity Framework contexts</span></span>

<span data-ttu-id="565ea-855">Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="565ea-856">O tempo de vida padrão será definido como escopo se um tempo de vida não for especificado por uma sobrecarga [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) ao registrar o contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="565ea-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="565ea-857">Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="565ea-858">Opções de tempo de vida e de registro</span><span class="sxs-lookup"><span data-stu-id="565ea-858">Lifetime and registration options</span></span>

<span data-ttu-id="565ea-859">Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="565ea-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="565ea-860">Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:</span><span class="sxs-lookup"><span data-stu-id="565ea-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="565ea-861">As interfaces são implementadas na classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="565ea-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="565ea-862">O construtor `Operation` gera um GUID, caso um não seja fornecido:</span><span class="sxs-lookup"><span data-stu-id="565ea-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="565ea-863">Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos.</span><span class="sxs-lookup"><span data-stu-id="565ea-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="565ea-864">Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.</span><span class="sxs-lookup"><span data-stu-id="565ea-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="565ea-865">Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="565ea-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="565ea-866">`OperationService` recebe uma nova instância da classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="565ea-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="565ea-867">A nova instância produz outro `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="565ea-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="565ea-868">Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="565ea-869">Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="565ea-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="565ea-870">Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="565ea-871">Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:</span><span class="sxs-lookup"><span data-stu-id="565ea-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="565ea-872">O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="565ea-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="565ea-873">Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).</span><span class="sxs-lookup"><span data-stu-id="565ea-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="565ea-874">O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais.</span><span class="sxs-lookup"><span data-stu-id="565ea-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="565ea-875">O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="565ea-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="565ea-876">Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:</span><span class="sxs-lookup"><span data-stu-id="565ea-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="565ea-877">As duas saídas a seguir mostram os resultados das duas solicitações:</span><span class="sxs-lookup"><span data-stu-id="565ea-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="565ea-878">**Primeira solicitação:**</span><span class="sxs-lookup"><span data-stu-id="565ea-878">**First request:**</span></span>

<span data-ttu-id="565ea-879">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="565ea-879">Controller operations:</span></span>

<span data-ttu-id="565ea-880">Transitória: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="565ea-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="565ea-881">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="565ea-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="565ea-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-883">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-884">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="565ea-884">`OperationService` operations:</span></span>

<span data-ttu-id="565ea-885">Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="565ea-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="565ea-886">Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="565ea-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="565ea-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-888">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-889">**Segunda solicitação:**</span><span class="sxs-lookup"><span data-stu-id="565ea-889">**Second request:**</span></span>

<span data-ttu-id="565ea-890">Operações do controlador:</span><span class="sxs-lookup"><span data-stu-id="565ea-890">Controller operations:</span></span>

<span data-ttu-id="565ea-891">Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="565ea-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="565ea-892">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="565ea-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="565ea-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-894">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-895">`OperationService` operações:</span><span class="sxs-lookup"><span data-stu-id="565ea-895">`OperationService` operations:</span></span>

<span data-ttu-id="565ea-896">Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="565ea-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="565ea-897">Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="565ea-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="565ea-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="565ea-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="565ea-899">Instância: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="565ea-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="565ea-900">Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:</span><span class="sxs-lookup"><span data-stu-id="565ea-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="565ea-901">Os objetos *transitórios* sempre são diferentes.</span><span class="sxs-lookup"><span data-stu-id="565ea-901">*Transient* objects are always different.</span></span> <span data-ttu-id="565ea-902">O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="565ea-903">Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="565ea-904">Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.</span><span class="sxs-lookup"><span data-stu-id="565ea-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="565ea-905">Os pbjetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="565ea-906">Chamar os serviços desde o principal</span><span class="sxs-lookup"><span data-stu-id="565ea-906">Call services from main</span></span>

<span data-ttu-id="565ea-907">Crie um <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> com [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver um serviço com escopo dentro do escopo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="565ea-908">Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="565ea-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="565ea-909">O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="565ea-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="565ea-910">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="565ea-910">Scope validation</span></span>

<span data-ttu-id="565ea-911">Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="565ea-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="565ea-912">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="565ea-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="565ea-913">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="565ea-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="565ea-914">O provedor de serviços raiz é criado quando <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="565ea-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="565ea-915">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="565ea-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="565ea-916">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="565ea-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="565ea-917">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="565ea-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="565ea-918">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="565ea-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="565ea-919">Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="565ea-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="565ea-920">Serviços de solicitação</span><span class="sxs-lookup"><span data-stu-id="565ea-920">Request Services</span></span>

<span data-ttu-id="565ea-921">Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="565ea-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="565ea-922">Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="565ea-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="565ea-923">Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="565ea-924">Em geral, o aplicativo não deve usar essas propriedades diretamente.</span><span class="sxs-lookup"><span data-stu-id="565ea-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="565ea-925">Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="565ea-926">Isso resulta em classes que são mais fáceis de testar.</span><span class="sxs-lookup"><span data-stu-id="565ea-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="565ea-927">Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="565ea-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="565ea-928">Projetar serviços para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="565ea-928">Design services for dependency injection</span></span>

<span data-ttu-id="565ea-929">As melhores práticas são:</span><span class="sxs-lookup"><span data-stu-id="565ea-929">Best practices are to:</span></span>

* <span data-ttu-id="565ea-930">Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.</span><span class="sxs-lookup"><span data-stu-id="565ea-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="565ea-931">Evite classes e membros com estado e estáticos.</span><span class="sxs-lookup"><span data-stu-id="565ea-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="565ea-932">Crie aplicativos para usar os serviços singleton, o que evita a criação de estado global.</span><span class="sxs-lookup"><span data-stu-id="565ea-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="565ea-933">Evite a instanciação direta das classes dependentes em serviços.</span><span class="sxs-lookup"><span data-stu-id="565ea-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="565ea-934">A instanciação direta acopla o código a uma implementação específica.</span><span class="sxs-lookup"><span data-stu-id="565ea-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="565ea-935">Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.</span><span class="sxs-lookup"><span data-stu-id="565ea-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="565ea-936">Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="565ea-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="565ea-937">Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe.</span><span class="sxs-lookup"><span data-stu-id="565ea-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="565ea-938">Tenha em mente que as classes Razor de modelo de página de páginas e as classes do controlador MVC devem se concentrar nas preocupações da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="565ea-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="565ea-939">Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="565ea-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="565ea-940">Descarte de serviços</span><span class="sxs-lookup"><span data-stu-id="565ea-940">Disposal of services</span></span>

<span data-ttu-id="565ea-941">O contêiner chama <xref:System.IDisposable.Dispose*> para os tipos <xref:System.IDisposable> criados por ele.</span><span class="sxs-lookup"><span data-stu-id="565ea-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="565ea-942">Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="565ea-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="565ea-943">No exemplo a seguir, os serviços são criados pelo contêiner de serviço e descartados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="565ea-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="565ea-944">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="565ea-944">In the following example:</span></span>

* <span data-ttu-id="565ea-945">As instâncias de serviço não são criadas pelo contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="565ea-946">Os tempos de vida de serviço pretendidos não são conhecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="565ea-947">A estrutura não descarta os serviços automaticamente.</span><span class="sxs-lookup"><span data-stu-id="565ea-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="565ea-948">Se os serviços não forem explicitamente descartados no código do desenvolvedor, eles persistirão até que o aplicativo seja desligado.</span><span class="sxs-lookup"><span data-stu-id="565ea-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="565ea-949">Substituição do contêiner de serviço padrão</span><span class="sxs-lookup"><span data-stu-id="565ea-949">Default service container replacement</span></span>

<span data-ttu-id="565ea-950">O contêiner de serviço interno foi projetado para atender às necessidades da estrutura e da maioria dos aplicativos de consumidor.</span><span class="sxs-lookup"><span data-stu-id="565ea-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="565ea-951">É recomendável usar o contêiner interno, a menos que você precise de um recurso específico ao qual o contêiner interno não ofereça suporte, como:</span><span class="sxs-lookup"><span data-stu-id="565ea-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="565ea-952">Injeção de propriedade</span><span class="sxs-lookup"><span data-stu-id="565ea-952">Property injection</span></span>
* <span data-ttu-id="565ea-953">Injeção com base no nome</span><span class="sxs-lookup"><span data-stu-id="565ea-953">Injection based on name</span></span>
* <span data-ttu-id="565ea-954">Contêineres filho</span><span class="sxs-lookup"><span data-stu-id="565ea-954">Child containers</span></span>
* <span data-ttu-id="565ea-955">Gerenciamento de tempo de vida personalizado</span><span class="sxs-lookup"><span data-stu-id="565ea-955">Custom lifetime management</span></span>
* <span data-ttu-id="565ea-956">Suporte de `Func<T>` para inicialização lenta</span><span class="sxs-lookup"><span data-stu-id="565ea-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="565ea-957">Registro baseado em Convenção</span><span class="sxs-lookup"><span data-stu-id="565ea-957">Convention-based registration</span></span>

<span data-ttu-id="565ea-958">Os contêineres de terceiros a seguir podem ser usados com aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="565ea-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="565ea-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="565ea-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="565ea-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="565ea-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="565ea-961">Grace</span><span class="sxs-lookup"><span data-stu-id="565ea-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="565ea-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="565ea-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="565ea-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="565ea-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="565ea-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="565ea-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="565ea-965">Unity</span><span class="sxs-lookup"><span data-stu-id="565ea-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="565ea-966">Acesso thread-safe</span><span class="sxs-lookup"><span data-stu-id="565ea-966">Thread safety</span></span>

<span data-ttu-id="565ea-967">Crie serviços singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="565ea-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="565ea-968">Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.</span><span class="sxs-lookup"><span data-stu-id="565ea-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="565ea-969">O método de fábrica de um único serviço, como o segundo argumento para [addsingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), não precisa ser thread-safe.</span><span class="sxs-lookup"><span data-stu-id="565ea-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="565ea-970">Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.</span><span class="sxs-lookup"><span data-stu-id="565ea-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="565ea-971">Recomendações</span><span class="sxs-lookup"><span data-stu-id="565ea-971">Recommendations</span></span>

* <span data-ttu-id="565ea-972">A resolução de serviço baseada em `async/await` e `Task` não é compatível.</span><span class="sxs-lookup"><span data-stu-id="565ea-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="565ea-973">O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="565ea-974">Evite armazenar dados e a configuração diretamente no contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="565ea-975">Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço.</span><span class="sxs-lookup"><span data-stu-id="565ea-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="565ea-976">A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="565ea-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="565ea-977">Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto.</span><span class="sxs-lookup"><span data-stu-id="565ea-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="565ea-978">É melhor solicitar o item real por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="565ea-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="565ea-979">Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usar em outro lugar).</span><span class="sxs-lookup"><span data-stu-id="565ea-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="565ea-980">Evite usar o *padrão de localizador de serviço*, que combina a [inversão de estratégias de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="565ea-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="565ea-981">Não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando você pode usar di em vez disso:</span><span class="sxs-lookup"><span data-stu-id="565ea-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="565ea-982">**Incorreto**</span><span class="sxs-lookup"><span data-stu-id="565ea-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="565ea-983">**Correto**:</span><span class="sxs-lookup"><span data-stu-id="565ea-983">**Correct**:</span></span>

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

  * <span data-ttu-id="565ea-984">Evite injetar uma fábrica que resolva dependências em tempo de execução usando <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="565ea-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="565ea-985">Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="565ea-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="565ea-986">Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação.</span><span class="sxs-lookup"><span data-stu-id="565ea-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="565ea-987">As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.</span><span class="sxs-lookup"><span data-stu-id="565ea-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="565ea-988">A DI é uma *alternativa* aos padrões de acesso a objeto estático/global.</span><span class="sxs-lookup"><span data-stu-id="565ea-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="565ea-989">Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.</span><span class="sxs-lookup"><span data-stu-id="565ea-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="565ea-990">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="565ea-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="565ea-991">Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)</span><span class="sxs-lookup"><span data-stu-id="565ea-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="565ea-992">Princípio de Dependências Explícitas</span><span class="sxs-lookup"><span data-stu-id="565ea-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="565ea-993">Inversão de Contêineres de Controle e o padrão de Injeção de Dependência (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="565ea-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="565ea-994">Como registrar um serviço com várias interfaces na DI do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="565ea-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
