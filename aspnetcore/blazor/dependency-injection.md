---
<span data-ttu-id="40b6e-101">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-103">'Blazor'</span></span>
- <span data-ttu-id="40b6e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-104">'Identity'</span></span>
- <span data-ttu-id="40b6e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-106">'Razor'</span></span>
- <span data-ttu-id="40b6e-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="40b6e-108">Injeção de dependência de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="40b6e-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="40b6e-109">Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="40b6e-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="40b6e-110">dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40b6e-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40b6e-111">Os aplicativos podem usar serviços internos injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="40b6e-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="40b6e-112">Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="40b6e-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="40b6e-113">DI é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="40b6e-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="40b6e-114">Isso pode ser útil em Blazor aplicativos para:</span><span class="sxs-lookup"><span data-stu-id="40b6e-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="40b6e-115">Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .</span><span class="sxs-lookup"><span data-stu-id="40b6e-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="40b6e-116">Dissociar componentes de classes de serviço concretas usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="40b6e-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="40b6e-117">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="40b6e-118">A interface é implementada por uma `DataAccess` classe concreta e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="40b6e-119">Quando um componente usa DI para receber uma `IDataAccess` implementação, o componente não é acoplado ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="40b6e-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="40b6e-120">A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="40b6e-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="40b6e-121">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="40b6e-121">Default services</span></span>

<span data-ttu-id="40b6e-122">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="40b6e-123">Serviço</span><span class="sxs-lookup"><span data-stu-id="40b6e-123">Service</span></span> | <span data-ttu-id="40b6e-124">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="40b6e-124">Lifetime</span></span> | <span data-ttu-id="40b6e-125">Descrição</span><span class="sxs-lookup"><span data-stu-id="40b6e-125">Description</span></span> |
| ---
<span data-ttu-id="40b6e-126">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-127">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-128">'Blazor'</span></span>
- <span data-ttu-id="40b6e-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-129">'Identity'</span></span>
- <span data-ttu-id="40b6e-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-131">'Razor'</span></span>
- <span data-ttu-id="40b6e-132">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-132">'SignalR' uid:</span></span> 

<span data-ttu-id="40b6e-133">---- | título do---: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-134">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-135">'Blazor'</span></span>
- <span data-ttu-id="40b6e-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-136">'Identity'</span></span>
- <span data-ttu-id="40b6e-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-138">'Razor'</span></span>
- <span data-ttu-id="40b6e-139">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40b6e-140">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-141">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-142">'Blazor'</span></span>
- <span data-ttu-id="40b6e-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-143">'Identity'</span></span>
- <span data-ttu-id="40b6e-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-145">'Razor'</span></span>
- <span data-ttu-id="40b6e-146">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-146">'SignalR' uid:</span></span> 

<span data-ttu-id="40b6e-147">---- | título do---: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-148">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-149">'Blazor'</span></span>
- <span data-ttu-id="40b6e-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-150">'Identity'</span></span>
- <span data-ttu-id="40b6e-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-152">'Razor'</span></span>
- <span data-ttu-id="40b6e-153">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40b6e-154">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-155">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-156">'Blazor'</span></span>
- <span data-ttu-id="40b6e-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-157">'Identity'</span></span>
- <span data-ttu-id="40b6e-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-159">'Razor'</span></span>
- <span data-ttu-id="40b6e-160">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40b6e-161">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-162">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-163">'Blazor'</span></span>
- <span data-ttu-id="40b6e-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-164">'Identity'</span></span>
- <span data-ttu-id="40b6e-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-166">'Razor'</span></span>
- <span data-ttu-id="40b6e-167">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-167">'SignalR' uid:</span></span> 

<span data-ttu-id="40b6e-168">------ | | <xref:System.Net.Http.HttpClient> | Transitório | Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="40b6e-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="40b6e-169">A instância do `HttpClient` em um Blazor aplicativo Webassembly usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="40b6e-169">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="40b6e-170">Os aplicativos de servidor não incluem um `HttpClient` configurado como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6e-170"> Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="40b6e-171">Forneça um `HttpClient` para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="40b6e-171">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="40b6e-172">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="40b6e-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="40b6e-173">| | `IJSRuntime` | Singleton ( Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="40b6e-173">| | `IJSRuntime` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="40b6e-174">Com escopo ( Blazor servidor) | Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="40b6e-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="40b6e-175">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="40b6e-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="40b6e-176">| | `NavigationManager` | Singleton ( Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="40b6e-176">| | `NavigationManager` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="40b6e-177">Com escopo ( Blazor servidor) | Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="40b6e-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="40b6e-178">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="40b6e-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="40b6e-179">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="40b6e-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="40b6e-180">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="40b6e-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="40b6e-181">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="40b6e-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="40b6e-182">Webassembly</span><span class="sxs-lookup"><span data-stu-id="40b6e-182"> WebAssembly</span></span>

<span data-ttu-id="40b6e-183">Configure os serviços para a coleção de serviços do aplicativo no `Main` método de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="40b6e-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="40b6e-184">No exemplo a seguir, a `MyDependency` implementação está registrada para `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="40b6e-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="40b6e-185">Depois que o host é criado, os serviços podem ser acessados do escopo de DI raiz antes de qualquer componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="40b6e-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="40b6e-186">Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:</span><span class="sxs-lookup"><span data-stu-id="40b6e-186">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="40b6e-187">O host também fornece uma instância de configuração central para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="40b6e-188">Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, *appSettings. JSON*) para `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="40b6e-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor<span data-ttu-id="40b6e-189">Servidor</span><span class="sxs-lookup"><span data-stu-id="40b6e-189"> Server</span></span>

<span data-ttu-id="40b6e-190">Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="40b6e-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="40b6e-191">O `ConfigureServices` método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , que é uma lista de objetos do descritor de serviço ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="40b6e-191">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="40b6e-192">Os serviços são adicionados fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="40b6e-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="40b6e-193">O exemplo a seguir demonstra o conceito com a `IDataAccess` interface e sua implementação concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="40b6e-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="40b6e-194">Tempo de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="40b6e-194">Service lifetime</span></span>

<span data-ttu-id="40b6e-195">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="40b6e-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="40b6e-196">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="40b6e-196">Lifetime</span></span> | <span data-ttu-id="40b6e-197">Descrição</span><span class="sxs-lookup"><span data-stu-id="40b6e-197">Description</span></span> |
| ---
<span data-ttu-id="40b6e-198">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-199">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-200">'Blazor'</span></span>
- <span data-ttu-id="40b6e-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-201">'Identity'</span></span>
- <span data-ttu-id="40b6e-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-203">'Razor'</span></span>
- <span data-ttu-id="40b6e-204">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40b6e-205">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-206">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-207">'Blazor'</span></span>
- <span data-ttu-id="40b6e-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-208">'Identity'</span></span>
- <span data-ttu-id="40b6e-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-210">'Razor'</span></span>
- <span data-ttu-id="40b6e-211">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-211">'SignalR' uid:</span></span> 

<span data-ttu-id="40b6e-212">---- | título do---: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-213">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-214">'Blazor'</span></span>
- <span data-ttu-id="40b6e-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-215">'Identity'</span></span>
- <span data-ttu-id="40b6e-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-217">'Razor'</span></span>
- <span data-ttu-id="40b6e-218">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40b6e-219">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-220">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-221">'Blazor'</span></span>
- <span data-ttu-id="40b6e-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-222">'Identity'</span></span>
- <span data-ttu-id="40b6e-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-224">'Razor'</span></span>
- <span data-ttu-id="40b6e-225">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="40b6e-226">Título: ' ASP.NET Core Blazor injeção de dependência ' autor: Descrição: ' Veja como os Blazor aplicativos podem injetar serviços em componentes. '</span><span class="sxs-lookup"><span data-stu-id="40b6e-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="40b6e-227">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="40b6e-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40b6e-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-228">'Blazor'</span></span>
- <span data-ttu-id="40b6e-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40b6e-229">'Identity'</span></span>
- <span data-ttu-id="40b6e-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40b6e-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="40b6e-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40b6e-231">'Razor'</span></span>
- <span data-ttu-id="40b6e-232">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="40b6e-232">'SignalR' uid:</span></span> 

<span data-ttu-id="40b6e-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Os aplicativos Webassembly não têm atualmente um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="40b6e-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="40b6e-234">`Scoped`-serviços registrados se comportam como `Singleton` serviços.</span><span class="sxs-lookup"><span data-stu-id="40b6e-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="40b6e-235">No entanto, o Blazor modelo de hospedagem do servidor dá suporte ao `Scoped` tempo de vida.</span><span class="sxs-lookup"><span data-stu-id="40b6e-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="40b6e-236">Em Blazor aplicativos de servidor, um registro de serviço com escopo é definido para a *conexão*.</span><span class="sxs-lookup"><span data-stu-id="40b6e-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="40b6e-237">Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="40b6e-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="40b6e-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="40b6e-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="40b6e-239">Todos os componentes que exigem um `Singleton` serviço recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="40b6e-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="40b6e-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="40b6e-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="40b6e-241">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40b6e-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="40b6e-242">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="40b6e-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="40b6e-243">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="40b6e-243">Request a service in a component</span></span>

<span data-ttu-id="40b6e-244">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando a diretiva de [ \@ inserção](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="40b6e-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="40b6e-245">`@inject`tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="40b6e-245">`@inject` has two parameters:</span></span>

* <span data-ttu-id="40b6e-246">Digite &ndash; o tipo do serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="40b6e-246">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="40b6e-247">Propriedade &ndash; o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="40b6e-247">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="40b6e-248">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="40b6e-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="40b6e-249">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="40b6e-249">The compiler creates the property.</span></span>

<span data-ttu-id="40b6e-250">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="40b6e-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="40b6e-251">Use várias `@inject` instruções para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="40b6e-251">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="40b6e-252">O exemplo a seguir mostra como usar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="40b6e-252">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="40b6e-253">A implementação do serviço `Services.IDataAccess` é injetada na Propriedade do componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="40b6e-254">Observe como o código está usando apenas a `IDataAccess` abstração:</span><span class="sxs-lookup"><span data-stu-id="40b6e-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="40b6e-255">Internamente, a propriedade gerada ( `DataRepository` ) usa o `InjectAttribute` atributo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-255">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="40b6e-256">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="40b6e-257">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente `InjectAttribute` :</span><span class="sxs-lookup"><span data-stu-id="40b6e-257">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="40b6e-258">Em componentes derivados da classe base, a `@inject` diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="40b6e-258">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="40b6e-259">O `InjectAttribute` da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="40b6e-259">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="40b6e-260">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="40b6e-260">Use DI in services</span></span>

<span data-ttu-id="40b6e-261">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="40b6e-261">Complex services might require additional services.</span></span> <span data-ttu-id="40b6e-262">No exemplo anterior, `DataAccess` pode exigir o `HttpClient` serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6e-262">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="40b6e-263">`@inject`(ou o `InjectAttribute` ) Não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="40b6e-263">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="40b6e-264">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="40b6e-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="40b6e-265">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="40b6e-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="40b6e-266">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="40b6e-267">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="40b6e-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="40b6e-268">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="40b6e-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="40b6e-269">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6e-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="40b6e-270">O Construtor aplicável deve ser *público*.</span><span class="sxs-lookup"><span data-stu-id="40b6e-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="40b6e-271">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="40b6e-271">One applicable constructor must exist.</span></span> <span data-ttu-id="40b6e-272">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="40b6e-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="40b6e-273">Classes de componente base do utilitário para gerenciar um escopo de DI</span><span class="sxs-lookup"><span data-stu-id="40b6e-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="40b6e-274">Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="40b6e-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="40b6e-275">Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI.</span><span class="sxs-lookup"><span data-stu-id="40b6e-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="40b6e-276">Em Blazor aplicativos de servidor, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="40b6e-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="40b6e-277">Em Blazor aplicativos Webassembly, os serviços registrados com um tempo de vida no escopo são tratados como singletons, portanto, eles vivem mais do que os serviços com escopo em aplicativos ASP.NET Core típicos.</span><span class="sxs-lookup"><span data-stu-id="40b6e-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="40b6e-278">Uma abordagem que limita um tempo de vida do serviço em Blazor aplicativos é o uso do `OwningComponentBase` tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-278">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="40b6e-279">`OwningComponentBase`é um tipo abstrato derivado de `ComponentBase` que cria um escopo de di correspondente ao tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-279">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="40b6e-280">Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="40b6e-281">Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados.</span><span class="sxs-lookup"><span data-stu-id="40b6e-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="40b6e-282">Isso pode ser útil para serviços que:</span><span class="sxs-lookup"><span data-stu-id="40b6e-282">This can be useful for services that:</span></span>

* <span data-ttu-id="40b6e-283">Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.</span><span class="sxs-lookup"><span data-stu-id="40b6e-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="40b6e-284">Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.</span><span class="sxs-lookup"><span data-stu-id="40b6e-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="40b6e-285">Duas versões do `OwningComponentBase` tipo estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="40b6e-285">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="40b6e-286">`OwningComponentBase`é um filho abstrato e descartável do `ComponentBase` tipo com uma `ScopedServices` propriedade protegida do tipo `IServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-286">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="40b6e-287">Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="40b6e-288">Os serviços de DI injetados no componente usando `@inject` o ou o `InjectAttribute` ( `[Inject]` ) não são criados no escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-288">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="40b6e-289">Para usar o escopo do componente, os serviços devem ser resolvidos usando o `ScopedServices.GetRequiredService` ou o `ScopedServices.GetService` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-289">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="40b6e-290">Todos os serviços resolvidos usando o `ScopedServices` provedor têm suas dependências fornecidas do mesmo escopo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-290">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="40b6e-291">`OwningComponentBase<T>`deriva de `OwningComponentBase` e adiciona uma propriedade `Service` que retorna uma instância do do `T` provedor de injeção de escopo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-291">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="40b6e-292">Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do `IServiceProvider` quando há um serviço primário que o aplicativo requer do contêiner di usando o escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-292">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="40b6e-293">A `ScopedServices` propriedade está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="40b6e-293">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="40b6e-294">Uso de Entity Framework DbContext de DI</span><span class="sxs-lookup"><span data-stu-id="40b6e-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="40b6e-295">Um tipo de serviço comum a ser recuperado de DI em aplicativos Web é Entity Framework (EF) `DbContext` objetos.</span><span class="sxs-lookup"><span data-stu-id="40b6e-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="40b6e-296">O registro de serviços do EF usando `IServiceCollection.AddDbContext` o adiciona o `DbContext` como um serviço com escopo por padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6e-296">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="40b6e-297">O registro como um serviço com escopo pode levar a problemas em Blazor aplicativos porque faz com que as `DbContext` instâncias sejam de longa duração e compartilhadas entre o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-297">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="40b6e-298">`DbContext`Não é thread-safe e não deve ser usado simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="40b6e-298">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="40b6e-299">Dependendo do aplicativo, o uso do `OwningComponentBase` para limitar o escopo de um `DbContext` a um único componente *pode* resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="40b6e-299">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="40b6e-300">Se um componente não usa um `DbContext` em paralelo, derivar o componente de `OwningComponentBase` e recuperar o `DbContext` de `ScopedServices` é suficiente porque garante que:</span><span class="sxs-lookup"><span data-stu-id="40b6e-300">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="40b6e-301">Componentes separados não compartilham um `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-301">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="40b6e-302">O `DbContext` reside apenas desde que o componente dependa dele.</span><span class="sxs-lookup"><span data-stu-id="40b6e-302">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="40b6e-303">Se um único componente pode usar um `DbContext` simultaneamente (por exemplo, sempre que um usuário seleciona um botão), mesmo usando o `OwningComponentBase` não evita problemas com operações simultâneas do EF.</span><span class="sxs-lookup"><span data-stu-id="40b6e-303">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="40b6e-304">Nesse caso, use outro `DbContext` para cada operação lógica do EF.</span><span class="sxs-lookup"><span data-stu-id="40b6e-304">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="40b6e-305">Use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="40b6e-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="40b6e-306">Crie o `DbContext` usando diretamente `DbContextOptions<TContext>` como um argumento, que pode ser recuperado de di e é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="40b6e-306">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="40b6e-307">Registre o `DbContext` no contêiner de serviço com um tempo de vida transitório:</span><span class="sxs-lookup"><span data-stu-id="40b6e-307">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="40b6e-308">Ao registrar o contexto, use `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-308">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="40b6e-309">O `AddDbContext` método de extensão usa dois parâmetros opcionais do tipo `ServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-309">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="40b6e-310">Para usar essa abordagem, somente o `contextLifetime` parâmetro precisa ser `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-310">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="40b6e-311">`optionsLifetime`pode manter seu valor padrão de `ServiceLifetime.Scoped` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-311">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="40b6e-312">O transitório `DbContext` pode ser injetado como normal (usando `@inject` ) em componentes que não executarão várias operações de EF em paralelo.</span><span class="sxs-lookup"><span data-stu-id="40b6e-312">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="40b6e-313">Os que podem executar várias operações de EF simultaneamente podem solicitar `DbContext` objetos separados para cada operação em paralelo usando `IServiceProvider.GetRequiredService` .</span><span class="sxs-lookup"><span data-stu-id="40b6e-313">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="40b6e-314">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40b6e-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
