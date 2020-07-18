---
title: Injeção de dependência de ASP.NET Core Blazor
author: guardrex
description: Saiba como os Blazor aplicativos podem injetar serviços em componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 07fe7d4b64c84956be44e7d3ac0b1d8687b085c6
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445158"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="46aef-103">Injeção de dependência de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="46aef-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="46aef-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="46aef-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="46aef-105">dá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="46aef-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46aef-106">Os aplicativos podem usar serviços internos injetando-os em componentes.</span><span class="sxs-lookup"><span data-stu-id="46aef-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="46aef-107">Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="46aef-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="46aef-108">DI é uma técnica para acessar os serviços configurados em um local central.</span><span class="sxs-lookup"><span data-stu-id="46aef-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="46aef-109">Isso pode ser útil em Blazor aplicativos para:</span><span class="sxs-lookup"><span data-stu-id="46aef-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="46aef-110">Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .</span><span class="sxs-lookup"><span data-stu-id="46aef-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="46aef-111">Dissociar componentes de classes de serviço concretas usando abstrações de referência.</span><span class="sxs-lookup"><span data-stu-id="46aef-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="46aef-112">Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46aef-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="46aef-113">A interface é implementada por uma `DataAccess` classe concreta e registrada como um serviço no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46aef-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="46aef-114">Quando um componente usa DI para receber uma `IDataAccess` implementação, o componente não é acoplado ao tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="46aef-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="46aef-115">A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.</span><span class="sxs-lookup"><span data-stu-id="46aef-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="46aef-116">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="46aef-116">Default services</span></span>

<span data-ttu-id="46aef-117">Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46aef-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="46aef-118">Serviço</span><span class="sxs-lookup"><span data-stu-id="46aef-118">Service</span></span> | <span data-ttu-id="46aef-119">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="46aef-119">Lifetime</span></span> | <span data-ttu-id="46aef-120">Descrição</span><span class="sxs-lookup"><span data-stu-id="46aef-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="46aef-121">Com escopo</span><span class="sxs-lookup"><span data-stu-id="46aef-121">Scoped</span></span> | <span data-ttu-id="46aef-122">Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.</span><span class="sxs-lookup"><span data-stu-id="46aef-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="46aef-123">A instância do <xref:System.Net.Http.HttpClient> em um Blazor WebAssembly aplicativo usa o navegador para manipular o tráfego HTTP em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="46aef-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor Server<span data-ttu-id="46aef-124">os aplicativos não incluem um <xref:System.Net.Http.HttpClient> configurado como um serviço por padrão.</span><span class="sxs-lookup"><span data-stu-id="46aef-124"> apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="46aef-125">Forneça um <xref:System.Net.Http.HttpClient> para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46aef-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="46aef-126">Para obter mais informações, consulte <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="46aef-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="46aef-127">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="46aef-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="46aef-128">Com escopo ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="46aef-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="46aef-129">Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas.</span><span class="sxs-lookup"><span data-stu-id="46aef-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="46aef-130">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="46aef-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="46aef-131">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="46aef-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="46aef-132">Com escopo ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="46aef-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="46aef-133">Contém auxiliares para trabalhar com URIs e estado de navegação.</span><span class="sxs-lookup"><span data-stu-id="46aef-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="46aef-134">Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="46aef-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="46aef-135">Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela.</span><span class="sxs-lookup"><span data-stu-id="46aef-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="46aef-136">Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.</span><span class="sxs-lookup"><span data-stu-id="46aef-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="46aef-137">Adicionar serviços a um aplicativo</span><span class="sxs-lookup"><span data-stu-id="46aef-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="46aef-138">Configure serviços para a coleção de serviços do aplicativo no `Main` método de `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="46aef-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="46aef-139">No exemplo a seguir, a `MyDependency` implementação está registrada para `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="46aef-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="46aef-140">Depois que o host é criado, os serviços podem ser acessados do escopo de DI raiz antes de qualquer componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="46aef-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="46aef-141">Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:</span><span class="sxs-lookup"><span data-stu-id="46aef-141">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="46aef-142">O host também fornece uma instância de configuração central para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46aef-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="46aef-143">Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, `appsettings.json` ) para `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="46aef-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="46aef-144">Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="46aef-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="46aef-145">O <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , que é uma lista de objetos do descritor de serviço ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ).</span><span class="sxs-lookup"><span data-stu-id="46aef-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="46aef-146">Os serviços são adicionados ao `ConfigureServices` método fornecendo descritores de serviço à coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="46aef-146">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="46aef-147">O exemplo a seguir demonstra o conceito com a `IDataAccess` interface e sua implementação concreta `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="46aef-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="46aef-148">Tempo de vida do serviço</span><span class="sxs-lookup"><span data-stu-id="46aef-148">Service lifetime</span></span>

<span data-ttu-id="46aef-149">Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="46aef-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="46aef-150">Tempo de vida</span><span class="sxs-lookup"><span data-stu-id="46aef-150">Lifetime</span></span> | <span data-ttu-id="46aef-151">Descrição</span><span class="sxs-lookup"><span data-stu-id="46aef-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly<span data-ttu-id="46aef-152">Atualmente, os aplicativos não têm um conceito de escopos de DI.</span><span class="sxs-lookup"><span data-stu-id="46aef-152"> apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="46aef-153">`Scoped`-serviços registrados se comportam como `Singleton` serviços.</span><span class="sxs-lookup"><span data-stu-id="46aef-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="46aef-154">No entanto, o Blazor Server modelo de hospedagem dá suporte ao `Scoped` tempo de vida.</span><span class="sxs-lookup"><span data-stu-id="46aef-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="46aef-155">Em Blazor Server aplicativos, um registro de serviço com escopo é definido para a *conexão*.</span><span class="sxs-lookup"><span data-stu-id="46aef-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="46aef-156">Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="46aef-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="46aef-157">DI cria uma *única instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="46aef-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="46aef-158">Todos os componentes que exigem um `Singleton` serviço recebem uma instância do mesmo serviço.</span><span class="sxs-lookup"><span data-stu-id="46aef-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="46aef-159">Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço.</span><span class="sxs-lookup"><span data-stu-id="46aef-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="46aef-160">O sistema de DI é baseado no sistema de injeção de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="46aef-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="46aef-161">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="46aef-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="46aef-162">Solicitar um serviço em um componente</span><span class="sxs-lookup"><span data-stu-id="46aef-162">Request a service in a component</span></span>

<span data-ttu-id="46aef-163">Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando a diretiva de [ \@ inserção](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="46aef-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="46aef-164">[`@inject`](xref:mvc/views/razor#inject)tem dois parâmetros:</span><span class="sxs-lookup"><span data-stu-id="46aef-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="46aef-165">Tipo: o tipo do serviço a injetar.</span><span class="sxs-lookup"><span data-stu-id="46aef-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="46aef-166">Propriedade: o nome da propriedade que recebe o serviço de aplicativo injetado.</span><span class="sxs-lookup"><span data-stu-id="46aef-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="46aef-167">A propriedade não requer criação manual.</span><span class="sxs-lookup"><span data-stu-id="46aef-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="46aef-168">O compilador cria a propriedade.</span><span class="sxs-lookup"><span data-stu-id="46aef-168">The compiler creates the property.</span></span>

<span data-ttu-id="46aef-169">Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="46aef-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="46aef-170">Use várias [`@inject`](xref:mvc/views/razor#inject) instruções para injetar serviços diferentes.</span><span class="sxs-lookup"><span data-stu-id="46aef-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="46aef-171">O exemplo a seguir mostra como usar [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="46aef-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="46aef-172">A implementação do serviço `Services.IDataAccess` é injetada na Propriedade do componente `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="46aef-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="46aef-173">Observe como o código está usando apenas a `IDataAccess` abstração:</span><span class="sxs-lookup"><span data-stu-id="46aef-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="46aef-174">Internamente, a propriedade gerada ( `DataRepository` ) usa o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="46aef-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="46aef-175">Normalmente, esse atributo não é usado diretamente.</span><span class="sxs-lookup"><span data-stu-id="46aef-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="46aef-176">Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo:</span><span class="sxs-lookup"><span data-stu-id="46aef-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="46aef-177">Em componentes derivados da classe base, a [`@inject`](xref:mvc/views/razor#inject) diretiva não é necessária.</span><span class="sxs-lookup"><span data-stu-id="46aef-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="46aef-178">O <xref:Microsoft.AspNetCore.Components.InjectAttribute> da classe base é suficiente:</span><span class="sxs-lookup"><span data-stu-id="46aef-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="46aef-179">Usar DI em serviços</span><span class="sxs-lookup"><span data-stu-id="46aef-179">Use DI in services</span></span>

<span data-ttu-id="46aef-180">Serviços complexos podem exigir serviços adicionais.</span><span class="sxs-lookup"><span data-stu-id="46aef-180">Complex services might require additional services.</span></span> <span data-ttu-id="46aef-181">No exemplo anterior, `DataAccess` pode exigir o <xref:System.Net.Http.HttpClient> serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="46aef-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="46aef-182">[`@inject`](xref:mvc/views/razor#inject)(ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo) não está disponível para uso em serviços.</span><span class="sxs-lookup"><span data-stu-id="46aef-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="46aef-183">A *injeção de Construtor* deve ser usada em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="46aef-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="46aef-184">Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço.</span><span class="sxs-lookup"><span data-stu-id="46aef-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="46aef-185">Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo.</span><span class="sxs-lookup"><span data-stu-id="46aef-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="46aef-186">No exemplo a seguir, o Construtor recebe um <xref:System.Net.Http.HttpClient> via di.</span><span class="sxs-lookup"><span data-stu-id="46aef-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="46aef-187"><xref:System.Net.Http.HttpClient>é um serviço padrão.</span><span class="sxs-lookup"><span data-stu-id="46aef-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="46aef-188">Pré-requisitos para injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="46aef-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="46aef-189">Um construtor deve existir cujos argumentos podem ser todos atendidos por DI.</span><span class="sxs-lookup"><span data-stu-id="46aef-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="46aef-190">Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.</span><span class="sxs-lookup"><span data-stu-id="46aef-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="46aef-191">O Construtor aplicável deve ser `public` .</span><span class="sxs-lookup"><span data-stu-id="46aef-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="46aef-192">Um Construtor aplicável deve existir.</span><span class="sxs-lookup"><span data-stu-id="46aef-192">One applicable constructor must exist.</span></span> <span data-ttu-id="46aef-193">No caso de uma ambiguidade, DI gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="46aef-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="46aef-194">Classes de componente base do utilitário para gerenciar um escopo de DI</span><span class="sxs-lookup"><span data-stu-id="46aef-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="46aef-195">Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="46aef-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="46aef-196">Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI.</span><span class="sxs-lookup"><span data-stu-id="46aef-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="46aef-197">Em Blazor Server aplicativos, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="46aef-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="46aef-198">Em Blazor WebAssembly aplicativos, os serviços registrados com um tempo de vida no escopo são tratados como singletons, portanto, eles vivem mais do que os serviços com escopo em aplicativos ASP.NET Core típicos.</span><span class="sxs-lookup"><span data-stu-id="46aef-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="46aef-199">Para detectar serviços descartáveis em um aplicativo, consulte a seção [detectar descartáveis transitórios](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="46aef-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="46aef-200">Uma abordagem que limita um tempo de vida do serviço em Blazor aplicativos é o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo.</span><span class="sxs-lookup"><span data-stu-id="46aef-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="46aef-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>é um tipo abstrato derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que cria um escopo de di correspondente ao tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="46aef-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="46aef-202">Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente.</span><span class="sxs-lookup"><span data-stu-id="46aef-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="46aef-203">Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados.</span><span class="sxs-lookup"><span data-stu-id="46aef-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="46aef-204">Isso pode ser útil para serviços que:</span><span class="sxs-lookup"><span data-stu-id="46aef-204">This can be useful for services that:</span></span>

* <span data-ttu-id="46aef-205">Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.</span><span class="sxs-lookup"><span data-stu-id="46aef-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="46aef-206">Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.</span><span class="sxs-lookup"><span data-stu-id="46aef-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="46aef-207">Duas versões do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="46aef-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="46aef-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>é um filho abstrato e descartável do <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo com uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade protegida do tipo <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="46aef-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="46aef-209">Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="46aef-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="46aef-210">Os serviços de DI injetados no componente usando [`@inject`](xref:mvc/views/razor#inject) ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo não são criados no escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="46aef-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="46aef-211">Para usar o escopo do componente, os serviços devem ser resolvidos usando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> ou o <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="46aef-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="46aef-212">Todos os serviços resolvidos usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provedor têm suas dependências fornecidas do mesmo escopo.</span><span class="sxs-lookup"><span data-stu-id="46aef-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="46aef-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e adiciona uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriedade que retorna uma instância do do `T` provedor de injeção de escopo.</span><span class="sxs-lookup"><span data-stu-id="46aef-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="46aef-214">Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do <xref:System.IServiceProvider> quando há um serviço primário que o aplicativo requer do contêiner di usando o escopo do componente.</span><span class="sxs-lookup"><span data-stu-id="46aef-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="46aef-215">A <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.</span><span class="sxs-lookup"><span data-stu-id="46aef-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="46aef-216">Uso de Entity Framework DbContext de DI</span><span class="sxs-lookup"><span data-stu-id="46aef-216">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="46aef-217">Um tipo de serviço comum a ser recuperado de DI em aplicativos Web é Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objetos.</span><span class="sxs-lookup"><span data-stu-id="46aef-217">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="46aef-218">O registro de serviços do EF usando <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o adiciona o <xref:Microsoft.EntityFrameworkCore.DbContext> como um serviço com escopo por padrão.</span><span class="sxs-lookup"><span data-stu-id="46aef-218">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="46aef-219">O registro como um serviço com escopo pode levar a problemas em Blazor aplicativos porque faz com que as <xref:Microsoft.EntityFrameworkCore.DbContext> instâncias sejam de longa duração e compartilhadas entre o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46aef-219">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="46aef-220"><xref:Microsoft.EntityFrameworkCore.DbContext>Não é thread-safe e não deve ser usado simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="46aef-220"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="46aef-221">Dependendo do aplicativo, o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> para limitar o escopo de um <xref:Microsoft.EntityFrameworkCore.DbContext> a um único componente *pode* resolver o problema.</span><span class="sxs-lookup"><span data-stu-id="46aef-221">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="46aef-222">Se um componente não usa um <xref:Microsoft.EntityFrameworkCore.DbContext> em paralelo, derivar o componente de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e recuperar o <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> é suficiente porque garante que:</span><span class="sxs-lookup"><span data-stu-id="46aef-222">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="46aef-223">Componentes separados não compartilham um <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="46aef-223">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="46aef-224">O <xref:Microsoft.EntityFrameworkCore.DbContext> reside apenas desde que o componente dependa dele.</span><span class="sxs-lookup"><span data-stu-id="46aef-224">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="46aef-225">Se um único componente pode usar um <xref:Microsoft.EntityFrameworkCore.DbContext> simultaneamente (por exemplo, sempre que um usuário seleciona um botão), mesmo usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> não evita problemas com operações simultâneas do EF.</span><span class="sxs-lookup"><span data-stu-id="46aef-225">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="46aef-226">Nesse caso, use outro <xref:Microsoft.EntityFrameworkCore.DbContext> para cada operação lógica do EF.</span><span class="sxs-lookup"><span data-stu-id="46aef-226">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="46aef-227">Use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="46aef-227">Use either of the following approaches:</span></span>

* <span data-ttu-id="46aef-228">Crie o <xref:Microsoft.EntityFrameworkCore.DbContext> usando diretamente <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> como um argumento, que pode ser recuperado de di e é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="46aef-228">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="46aef-229">Registre o <xref:Microsoft.EntityFrameworkCore.DbContext> no contêiner de serviço com um tempo de vida transitório:</span><span class="sxs-lookup"><span data-stu-id="46aef-229">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="46aef-230">Ao registrar o contexto, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="46aef-230">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="46aef-231">O <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> método de extensão usa dois parâmetros opcionais do tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="46aef-231">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="46aef-232">Para usar essa abordagem, somente o `contextLifetime` parâmetro precisa ser <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="46aef-232">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="46aef-233">`optionsLifetime`pode manter seu valor padrão de <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="46aef-233">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="46aef-234">O transitório <xref:Microsoft.EntityFrameworkCore.DbContext> pode ser injetado como normal (usando [`@inject`](xref:mvc/views/razor#inject) ) em componentes que não executarão várias operações de EF em paralelo.</span><span class="sxs-lookup"><span data-stu-id="46aef-234">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="46aef-235">Os que podem executar várias operações de EF simultaneamente podem solicitar <xref:Microsoft.EntityFrameworkCore.DbContext> objetos separados para cada operação em paralelo usando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="46aef-235">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

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

## <a name="detect-transient-disposables"></a><span data-ttu-id="46aef-236">Detectar descartáveis transitórios</span><span class="sxs-lookup"><span data-stu-id="46aef-236">Detect transient disposables</span></span>

<span data-ttu-id="46aef-237">Os exemplos a seguir mostram como detectar serviços descartáveis indetectáveis em um aplicativo que deve usar o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="46aef-237">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="46aef-238">Para obter mais informações, consulte a seção [classes de componente base do utilitário para gerenciar um escopo de di](#utility-base-component-classes-to-manage-a-di-scope) .</span><span class="sxs-lookup"><span data-stu-id="46aef-238">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="46aef-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="46aef-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="46aef-240">O `TransientDisposable` no exemplo a seguir é detectado ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="46aef-240">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="46aef-241">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="46aef-241">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="46aef-242">`Program`:</span><span class="sxs-lookup"><span data-stu-id="46aef-242">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="46aef-243">O `TransientDependency` no exemplo a seguir é detectado ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="46aef-243">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="46aef-244">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="46aef-244">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="46aef-245">`IDisposable`Diretrizes para instâncias transitórias e compartilhadas</span><span class="sxs-lookup"><span data-stu-id="46aef-245">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
