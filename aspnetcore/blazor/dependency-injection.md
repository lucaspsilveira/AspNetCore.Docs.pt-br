---
title: injeção Blazor de dependência do núcleo ASP.NET
author: guardrex
description: Veja Blazor como os aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658070"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="04c05-103">injeção de dependência de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="04c05-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="04c05-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="04c05-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="04c05-105">Blazor suporta [injeção de dependência (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="04c05-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="04c05-106">Os aplicativos podem usar serviços incorporados injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="04c05-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="04c05-107">Os aplicativos também podem definir e registrar serviços personalizados e disponibilizá-los em todo o aplicativo via DI.</span><span class="sxs-lookup"><span data-stu-id="04c05-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="04c05-108">DI é uma técnica de acesso a serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="04c05-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="04c05-109">Isso pode ser útil em aplicativos Blazor para:</span><span class="sxs-lookup"><span data-stu-id="04c05-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="04c05-110">Compartilhe uma única instância de uma classe de serviço em muitos componentes, conhecido como serviço *singleton.*</span><span class="sxs-lookup"><span data-stu-id="04c05-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="04c05-111">Desacoplar componentes de classes de serviço de concreto usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="04c05-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="04c05-112">Por exemplo, considere `IDataAccess` uma interface para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04c05-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="04c05-113">A interface é implementada `DataAccess` por uma classe de concreto e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04c05-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="04c05-114">Quando um componente usa `IDataAccess` DI para receber uma implementação, o componente não é acoplado ao tipo de concreto.</span><span class="sxs-lookup"><span data-stu-id="04c05-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="04c05-115">A implementação pode ser trocada, talvez para uma implementação simulada em testes unitários.</span><span class="sxs-lookup"><span data-stu-id="04c05-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="04c05-116">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="04c05-116">Default services</span></span>

<span data-ttu-id="04c05-117">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04c05-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="04c05-118">Serviço</span><span class="sxs-lookup"><span data-stu-id="04c05-118">Service</span></span> | <span data-ttu-id="04c05-119">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="04c05-119">Lifetime</span></span> | <span data-ttu-id="04c05-120">Descrição</span><span class="sxs-lookup"><span data-stu-id="04c05-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="04c05-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="04c05-121">Singleton</span></span> | <span data-ttu-id="04c05-122">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="04c05-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="04c05-123">A instância `HttpClient` de um aplicativo Blazor WebAssembly usa o navegador para lidar com o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="04c05-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="04c05-124">Os aplicativos do Blazor `HttpClient` Server não incluem um serviço configurado como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="04c05-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="04c05-125">Forneça `HttpClient` um aplicativo blazor server.</span><span class="sxs-lookup"><span data-stu-id="04c05-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="04c05-126">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="04c05-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="04c05-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="04c05-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="04c05-128">Escopo (Servidor Blazor)</span><span class="sxs-lookup"><span data-stu-id="04c05-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="04c05-129">Representa uma instância de um tempo de execução JavaScript onde as chamadas JavaScript são enviadas.</span><span class="sxs-lookup"><span data-stu-id="04c05-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="04c05-130">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="04c05-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="04c05-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="04c05-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="04c05-132">Escopo (Servidor Blazor)</span><span class="sxs-lookup"><span data-stu-id="04c05-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="04c05-133">Contém ajudantes para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="04c05-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="04c05-134">Para obter mais informações, consulte [uri e ajudantes de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="04c05-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="04c05-135">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="04c05-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="04c05-136">Se você usar um provedor de serviços personalizado e exigir qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="04c05-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="04c05-137">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="04c05-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="04c05-138">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="04c05-138">Blazor WebAssembly</span></span>

<span data-ttu-id="04c05-139">Configure serviços para a coleta `Main` de serviços do aplicativo no método de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="04c05-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="04c05-140">No exemplo a `MyDependency` seguir, a `IMyDependency`implementação é registrada para :</span><span class="sxs-lookup"><span data-stu-id="04c05-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="04c05-141">Uma vez que o host é construído, os serviços podem ser acessados a partir do escopo DI raiz antes que quaisquer componentes sejam renderizados.</span><span class="sxs-lookup"><span data-stu-id="04c05-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="04c05-142">Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:</span><span class="sxs-lookup"><span data-stu-id="04c05-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="04c05-143">O host também fornece uma instância de configuração central para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04c05-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="04c05-144">Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de `InitializeWeatherAsync`configuração padrão (por exemplo, *appsettings.json)* para :</span><span class="sxs-lookup"><span data-stu-id="04c05-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="04c05-145">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="04c05-145">Blazor Server</span></span>

<span data-ttu-id="04c05-146">Depois de criar um `Startup.ConfigureServices` novo aplicativo, examine o método:</span><span class="sxs-lookup"><span data-stu-id="04c05-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="04c05-147">O `ConfigureServices` método é <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>aprovado um , que é uma<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>lista de objetos descritores de serviço ( ).</span><span class="sxs-lookup"><span data-stu-id="04c05-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="04c05-148">Os serviços são adicionados fornecendo descritores de serviço à coleta de serviços.</span><span class="sxs-lookup"><span data-stu-id="04c05-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="04c05-149">O exemplo a seguir `IDataAccess` demonstra o conceito `DataAccess`com a interface e sua implementação concreta:</span><span class="sxs-lookup"><span data-stu-id="04c05-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="04c05-150">Vida útil do serviço</span><span class="sxs-lookup"><span data-stu-id="04c05-150">Service lifetime</span></span>

<span data-ttu-id="04c05-151">Os serviços podem ser configurados com as vidas de vida mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="04c05-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="04c05-152">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="04c05-152">Lifetime</span></span> | <span data-ttu-id="04c05-153">Descrição</span><span class="sxs-lookup"><span data-stu-id="04c05-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="04c05-154">Os aplicativos WebAssembly não têm atualmente um conceito de escopos DI.</span><span class="sxs-lookup"><span data-stu-id="04c05-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="04c05-155">`Scoped`- serviços registrados `Singleton` se comportam como serviços.</span><span class="sxs-lookup"><span data-stu-id="04c05-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="04c05-156">No entanto, o Blazor modelo `Scoped` de hospedagem do Servidor suporta a vida útil.</span><span class="sxs-lookup"><span data-stu-id="04c05-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="04c05-157">Nos Blazor aplicativos server, um registro de serviço escopo é escopo para a *conexão*.</span><span class="sxs-lookup"><span data-stu-id="04c05-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="04c05-158">Por essa razão, o uso de serviços escopo é preferido para serviços que devem ser escopo para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="04c05-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="04c05-159">O DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="04c05-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="04c05-160">Todos os componentes que requerem um `Singleton` serviço recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="04c05-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="04c05-161">Sempre que um componente obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma nova *instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="04c05-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="04c05-162">O sistema DI é baseado no sistema DI em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04c05-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="04c05-163">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="04c05-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="04c05-164">Solicite um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="04c05-164">Request a service in a component</span></span>

<span data-ttu-id="04c05-165">Após a adição dos serviços à coleta de [ \@](xref:mvc/views/razor#inject) serviços, injete os serviços nos componentes usando a diretiva Razor inject.</span><span class="sxs-lookup"><span data-stu-id="04c05-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="04c05-166">`@inject`tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="04c05-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="04c05-167">Digite &ndash; o tipo de serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="04c05-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="04c05-168">Propriedade &ndash; O nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="04c05-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="04c05-169">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="04c05-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="04c05-170">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="04c05-170">The compiler creates the property.</span></span>

<span data-ttu-id="04c05-171">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="04c05-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="04c05-172">Use `@inject` várias instruções para injetar diferentes serviços.</span><span class="sxs-lookup"><span data-stu-id="04c05-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="04c05-173">O exemplo a seguir mostra como usar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="04c05-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="04c05-174">A implementação `Services.IDataAccess` do serviço é injetada `DataRepository`na propriedade do componente.</span><span class="sxs-lookup"><span data-stu-id="04c05-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="04c05-175">Observe como o código `IDataAccess` está usando apenas a abstração:</span><span class="sxs-lookup"><span data-stu-id="04c05-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="04c05-176">Internamente, a propriedade`DataRepository`gerada `InjectAttribute` ( ) usa o atributo.</span><span class="sxs-lookup"><span data-stu-id="04c05-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="04c05-177">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="04c05-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="04c05-178">Se uma classe base for necessária para componentes e propriedades injetadas também `InjectAttribute`forem necessárias para a classe base, adicione manualmente:</span><span class="sxs-lookup"><span data-stu-id="04c05-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="04c05-179">Em componentes derivados da classe `@inject` base, a diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="04c05-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="04c05-180">A `InjectAttribute` classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="04c05-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="04c05-181">Use DI em serviços</span><span class="sxs-lookup"><span data-stu-id="04c05-181">Use DI in services</span></span>

<span data-ttu-id="04c05-182">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="04c05-182">Complex services might require additional services.</span></span> <span data-ttu-id="04c05-183">No exemplo anterior, `DataAccess` pode `HttpClient` exigir o serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="04c05-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="04c05-184">`@inject`(ou `InjectAttribute`o ) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="04c05-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="04c05-185">*Em* vez disso, deve-se usar a injeção de construção.</span><span class="sxs-lookup"><span data-stu-id="04c05-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="04c05-186">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="04c05-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="04c05-187">Quando a DI cria o serviço, reconhece os serviços que necessita na construtora e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="04c05-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="04c05-188">Pré-requisitos para injeção de construtores:</span><span class="sxs-lookup"><span data-stu-id="04c05-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="04c05-189">Um construtor deve existir cujos argumentos podem ser todos cumpridos pela DI.</span><span class="sxs-lookup"><span data-stu-id="04c05-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="04c05-190">Parâmetros adicionais não cobertos pelo DI são permitidos se especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="04c05-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="04c05-191">O construtor aplicável deve ser *público.*</span><span class="sxs-lookup"><span data-stu-id="04c05-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="04c05-192">Um construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="04c05-192">One applicable constructor must exist.</span></span> <span data-ttu-id="04c05-193">Em caso de ambiguidade, di lança uma exceção.</span><span class="sxs-lookup"><span data-stu-id="04c05-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="04c05-194">Classes de componentes de base de utilidade para gerenciar um escopo DI</span><span class="sxs-lookup"><span data-stu-id="04c05-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="04c05-195">Em ASP.NET de aplicativos Core, os serviços escopo são tipicamente escopo para a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="04c05-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="04c05-196">Após a solicitação ser concluída, quaisquer serviços escopo ou transitórios são descartados pelo sistema DI.</span><span class="sxs-lookup"><span data-stu-id="04c05-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="04c05-197">Nos Blazor aplicativos server, o escopo de solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e escopo vivendo muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="04c05-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="04c05-198">Nos Blazor aplicativos WebAssembly, os serviços registrados com uma vida útil escopo são tratados como singletons, de modo que eles vivem mais do que serviços escopo em aplicativos típicos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04c05-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="04c05-199">Uma abordagem que limita Blazor uma vida `OwningComponentBase` útil em aplicativos é o uso do tipo.</span><span class="sxs-lookup"><span data-stu-id="04c05-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="04c05-200">`OwningComponentBase`é um tipo abstrato derivado `ComponentBase` de que cria um escopo DI correspondente à vida útil do componente.</span><span class="sxs-lookup"><span data-stu-id="04c05-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="04c05-201">Usando este escopo, é possível usar serviços DI com uma vida útil escopo e fazê-los viver enquanto o componente.</span><span class="sxs-lookup"><span data-stu-id="04c05-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="04c05-202">Quando o componente é destruído, os serviços do provedor de serviços escopo do componente também são descartados.</span><span class="sxs-lookup"><span data-stu-id="04c05-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="04c05-203">Isso pode ser útil para serviços que:</span><span class="sxs-lookup"><span data-stu-id="04c05-203">This can be useful for services that:</span></span>

* <span data-ttu-id="04c05-204">Deve ser reutilizado dentro de um componente, pois a vida útil transitória é inadequada.</span><span class="sxs-lookup"><span data-stu-id="04c05-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="04c05-205">Não deve ser compartilhado entre componentes, pois a vida de singleton é inadequada.</span><span class="sxs-lookup"><span data-stu-id="04c05-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="04c05-206">Duas versões do `OwningComponentBase` tipo estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="04c05-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="04c05-207">`OwningComponentBase`é uma criança abstrata `ComponentBase` e descartável `ScopedServices` do `IServiceProvider`tipo com uma propriedade protegida do tipo.</span><span class="sxs-lookup"><span data-stu-id="04c05-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="04c05-208">Este provedor pode ser usado para resolver serviços que são escopo para a vida útil do componente.</span><span class="sxs-lookup"><span data-stu-id="04c05-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="04c05-209">Os serviços DI injetados `InjectAttribute` no`[Inject]`componente usando `@inject` ou () não são criados no escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="04c05-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="04c05-210">Para usar o escopo do componente, `ScopedServices.GetRequiredService` os `ScopedServices.GetService`serviços devem ser resolvidos usando ou .</span><span class="sxs-lookup"><span data-stu-id="04c05-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="04c05-211">Todos os serviços resolvidos usando o `ScopedServices` provedor têm suas dependências fornecidas a partir desse mesmo escopo.</span><span class="sxs-lookup"><span data-stu-id="04c05-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="04c05-212">`OwningComponentBase<T>`deriva `OwningComponentBase` e adiciona uma `Service` propriedade que `T` retorna uma instância do provedor DI escopo.</span><span class="sxs-lookup"><span data-stu-id="04c05-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="04c05-213">Esse tipo é uma maneira conveniente de acessar `IServiceProvider` serviços com escopo sem usar uma instância de quando há um serviço principal que o aplicativo requer do contêiner DI usando o escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="04c05-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="04c05-214">A `ScopedServices` propriedade está disponível, para que o aplicativo possa obter serviços de outros tipos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="04c05-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="04c05-215">Uso do Entity Framework DbContext da DI</span><span class="sxs-lookup"><span data-stu-id="04c05-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="04c05-216">Um tipo de serviço comum para recuperar do DI `DbContext` em aplicativos web são objetos Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="04c05-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="04c05-217">Registrar serviços EF `IServiceCollection.AddDbContext` `DbContext` usando adiciona o como um serviço escopo por padrão.</span><span class="sxs-lookup"><span data-stu-id="04c05-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="04c05-218">Registrar-se como um serviço escopo Blazor pode levar `DbContext` a problemas em aplicativos porque faz com que os casos sejam de longa duração e compartilhados em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04c05-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="04c05-219">`DbContext`não é seguro para rosca e não deve ser usado simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="04c05-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="04c05-220">Dependendo do aplicativo, `OwningComponentBase` usar para limitar `DbContext` o escopo de a a um único componente *pode* resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="04c05-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="04c05-221">Se um componente não `DbContext` usar um em paralelo, `OwningComponentBase` derivar `DbContext` o `ScopedServices` componente e recuperar o de é suficiente porque garante que:</span><span class="sxs-lookup"><span data-stu-id="04c05-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="04c05-222">Componentes separados não `DbContext`compartilham um .</span><span class="sxs-lookup"><span data-stu-id="04c05-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="04c05-223">As `DbContext` vidas só podem ser enquanto o componente depender dele.</span><span class="sxs-lookup"><span data-stu-id="04c05-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="04c05-224">Se um único componente `DbContext` pode usar um simultaneamente (por exemplo, toda `OwningComponentBase` vez que um usuário seleciona um botão), mesmo o uso não evita problemas com operações EF simultâneas.</span><span class="sxs-lookup"><span data-stu-id="04c05-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="04c05-225">Nesse caso, use `DbContext` um diferente para cada operação lógica de EF.</span><span class="sxs-lookup"><span data-stu-id="04c05-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="04c05-226">Use qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="04c05-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="04c05-227">Crie `DbContext` o `DbContextOptions<TContext>` uso direto como argumento, que pode ser recuperado de DI e é seguro para rosca.</span><span class="sxs-lookup"><span data-stu-id="04c05-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

* <span data-ttu-id="04c05-228">Registre `DbContext` o recipiente no serviço com uma vida útil transitória:</span><span class="sxs-lookup"><span data-stu-id="04c05-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="04c05-229">Ao registrar o contexto, use `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="04c05-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="04c05-230">O `AddDbContext` método de extensão `ServiceLifetime`leva dois parâmetros opcionais do tipo .</span><span class="sxs-lookup"><span data-stu-id="04c05-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="04c05-231">Para usar esta abordagem, apenas `contextLifetime` `ServiceLifetime.Transient`o parâmetro precisa ser .</span><span class="sxs-lookup"><span data-stu-id="04c05-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="04c05-232">`optionsLifetime`pode manter seu `ServiceLifetime.Scoped`valor padrão de .</span><span class="sxs-lookup"><span data-stu-id="04c05-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="04c05-233">O transitório `DbContext` pode ser injetado `@inject`normalmente (usando ) em componentes que não executarão várias operações EF em paralelo.</span><span class="sxs-lookup"><span data-stu-id="04c05-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="04c05-234">Aqueles que podem realizar várias operações EF simultaneamente podem solicitar objetos separados `DbContext` para cada operação paralela usando `IServiceProvider.GetRequiredService`.</span><span class="sxs-lookup"><span data-stu-id="04c05-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

## <a name="additional-resources"></a><span data-ttu-id="04c05-235">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="04c05-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
