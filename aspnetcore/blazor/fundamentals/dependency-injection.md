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
# <a name="aspnet-core-blazor-dependency-injection"></a>Injeção de dependência de ASP.NET Core Blazor

Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)

Blazordá suporte à [injeção de dependência (di)](xref:fundamentals/dependency-injection). Os aplicativos podem usar serviços internos injetando-os em componentes. Os aplicativos também podem definir e registrar serviços personalizados e torná-los disponíveis em todo o aplicativo por meio de DI.

DI é uma técnica para acessar os serviços configurados em um local central. Isso pode ser útil em Blazor aplicativos para:

* Compartilhe uma única instância de uma classe de serviço em vários componentes, conhecido como um serviço *singleton* .
* Dissociar componentes de classes de serviço concretas usando abstrações de referência. Por exemplo, considere uma interface `IDataAccess` para acessar dados no aplicativo. A interface é implementada por uma `DataAccess` classe concreta e registrada como um serviço no contêiner de serviço do aplicativo. Quando um componente usa DI para receber uma `IDataAccess` implementação, o componente não é acoplado ao tipo concreto. A implementação pode ser trocada, talvez para uma implementação fictícia em testes de unidade.

## <a name="default-services"></a>Serviços padrão

Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.

| Serviço | Tempo de vida | Descrição |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Com escopo | Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.<br><br>A instância do <xref:System.Net.Http.HttpClient> em um Blazor WebAssembly aplicativo usa o navegador para manipular o tráfego HTTP em segundo plano.<br><br>Blazor Serveros aplicativos não incluem um <xref:System.Net.Http.HttpClient> configurado como um serviço por padrão. Forneça um <xref:System.Net.Http.HttpClient> para um Blazor Server aplicativo.<br><br>Para obter mais informações, consulte <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor WebAssembly )<br>Com escopo ( Blazor Server ) | Representa uma instância de um tempo de execução JavaScript em que as chamadas JavaScript são expedidas. Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor WebAssembly )<br>Com escopo ( Blazor Server ) | Contém auxiliares para trabalhar com URIs e estado de navegação. Para obter mais informações, consulte [URI e auxiliares de estado de navegação](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela. Se você usar um provedor de serviços personalizado e precisar de qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.

## <a name="add-services-to-an-app"></a>Adicionar serviços a um aplicativo

### Blazor WebAssembly

Configure serviços para a coleção de serviços do aplicativo no `Main` método de `Program.cs` . No exemplo a seguir, a `MyDependency` implementação está registrada para `IMyDependency` :

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

Depois que o host é criado, os serviços podem ser acessados do escopo de DI raiz antes de qualquer componente ser renderizado. Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:

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

O host também fornece uma instância de configuração central para o aplicativo. Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de configuração padrão (por exemplo, `appsettings.json` ) para `InitializeWeatherAsync` :

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

Depois de criar um novo aplicativo, examine o `Startup.ConfigureServices` método:

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

O <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> método é passado um <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , que é uma lista de objetos do descritor de serviço ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ). Os serviços são adicionados ao `ConfigureServices` método fornecendo descritores de serviço à coleção de serviços. O exemplo a seguir demonstra o conceito com a `IDataAccess` interface e sua implementação concreta `DataAccess` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Tempo de vida do serviço

Os serviços podem ser configurados com os tempos de vida mostrados na tabela a seguir.

| Tempo de vida | Descrição |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssemblyAtualmente, os aplicativos não têm um conceito de escopos de DI. `Scoped`-serviços registrados se comportam como `Singleton` serviços. No entanto, o Blazor Server modelo de hospedagem dá suporte ao `Scoped` tempo de vida. Em Blazor Server aplicativos, um registro de serviço com escopo é definido para a *conexão*. Por esse motivo, o uso de serviços com escopo é preferencial para serviços que devem ser delimitados para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI cria uma *única instância* do serviço. Todos os componentes que exigem um `Singleton` serviço recebem uma instância do mesmo serviço. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Sempre que um componente Obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma *nova instância* do serviço. |

O sistema de DI é baseado no sistema de injeção de ASP.NET Core. Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Solicitar um serviço em um componente

Depois que os serviços forem adicionados à coleção de serviços, insira os serviços nos componentes usando a diretiva de [ \@ inserção](xref:mvc/views/razor#inject) Razor . [`@inject`](xref:mvc/views/razor#inject)tem dois parâmetros:

* Tipo: o tipo do serviço a injetar.
* Propriedade: o nome da propriedade que recebe o serviço de aplicativo injetado. A propriedade não requer criação manual. O compilador cria a propriedade.

Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.

Use várias [`@inject`](xref:mvc/views/razor#inject) instruções para injetar serviços diferentes.

O exemplo a seguir mostra como usar [`@inject`](xref:mvc/views/razor#inject) . A implementação do serviço `Services.IDataAccess` é injetada na Propriedade do componente `DataRepository` . Observe como o código está usando apenas a `IDataAccess` abstração:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Internamente, a propriedade gerada ( `DataRepository` ) usa o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo. Normalmente, esse atributo não é usado diretamente. Se uma classe base for necessária para componentes e propriedades injetadas também forem necessárias para a classe base, adicione manualmente o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

Em componentes derivados da classe base, a [`@inject`](xref:mvc/views/razor#inject) diretiva não é necessária. O <xref:Microsoft.AspNetCore.Components.InjectAttribute> da classe base é suficiente:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Usar DI em serviços

Serviços complexos podem exigir serviços adicionais. No exemplo anterior, `DataAccess` pode exigir o <xref:System.Net.Http.HttpClient> serviço padrão. [`@inject`](xref:mvc/views/razor#inject)(ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo) não está disponível para uso em serviços. A *injeção de Construtor* deve ser usada em seu lugar. Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço. Quando DI cria o serviço, ele reconhece os serviços que ele requer no construtor e os fornece de acordo. No exemplo a seguir, o Construtor recebe um <xref:System.Net.Http.HttpClient> via di. <xref:System.Net.Http.HttpClient>é um serviço padrão.

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Pré-requisitos para injeção de construtor:

* Um construtor deve existir cujos argumentos podem ser todos atendidos por DI. Parâmetros adicionais não cobertos por DI serão permitidos se eles especificarem valores padrão.
* O Construtor aplicável deve ser `public` .
* Um Construtor aplicável deve existir. No caso de uma ambiguidade, DI gera uma exceção.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de componente base do utilitário para gerenciar um escopo de DI

Em aplicativos ASP.NET Core, os serviços com escopo normalmente são incluídos no escopo da solicitação atual. Depois que a solicitação for concluída, todos os serviços com escopo ou transitórios serão descartados pelo sistema de DI. Em Blazor Server aplicativos, o escopo da solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e no escopo que vivem muito mais do que o esperado. Em Blazor WebAssembly aplicativos, os serviços registrados com um tempo de vida no escopo são tratados como singletons, portanto, eles vivem mais do que os serviços com escopo em aplicativos ASP.NET Core típicos.

> [!NOTE]
> Para detectar serviços descartáveis em um aplicativo, consulte a seção [detectar descartáveis transitórios](#detect-transient-disposables) .

Uma abordagem que limita um tempo de vida do serviço em Blazor aplicativos é o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo. <xref:Microsoft.AspNetCore.Components.OwningComponentBase>é um tipo abstrato derivado de <xref:Microsoft.AspNetCore.Components.ComponentBase> que cria um escopo de di correspondente ao tempo de vida do componente. Usando esse escopo, é possível usar os serviços de DI com um tempo de vida de escopo e tê-los ativos, desde que o componente. Quando o componente é destruído, os serviços do provedor de serviço no escopo do componente também são descartados. Isso pode ser útil para serviços que:

* Deve ser reutilizado dentro de um componente, pois o tempo de vida transitório é inadequado.
* Não devem ser compartilhados entre componentes, pois o tempo de vida singleton é inadequado.

Duas versões do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> tipo estão disponíveis:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>é um filho abstrato e descartável do <xref:Microsoft.AspNetCore.Components.ComponentBase> tipo com uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade protegida do tipo <xref:System.IServiceProvider> . Esse provedor pode ser usado para resolver serviços que têm o escopo definido para o tempo de vida do componente.

  Os serviços de DI injetados no componente usando [`@inject`](xref:mvc/views/razor#inject) ou o [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atributo não são criados no escopo do componente. Para usar o escopo do componente, os serviços devem ser resolvidos usando o <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> ou o <xref:System.IServiceProvider.GetService%2A> . Todos os serviços resolvidos usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provedor têm suas dependências fornecidas do mesmo escopo.

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>deriva de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e adiciona uma <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriedade que retorna uma instância do do `T` provedor de injeção de escopo. Esse tipo é uma maneira conveniente de acessar serviços com escopo sem usar uma instância do <xref:System.IServiceProvider> quando há um serviço primário que o aplicativo requer do contêiner di usando o escopo do componente. A <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriedade está disponível, portanto, o aplicativo pode obter serviços de outros tipos, se necessário.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Uso de Entity Framework DbContext de DI

Um tipo de serviço comum a ser recuperado de DI em aplicativos Web é Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objetos. O registro de serviços do EF usando <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> o adiciona o <xref:Microsoft.EntityFrameworkCore.DbContext> como um serviço com escopo por padrão. O registro como um serviço com escopo pode levar a problemas em Blazor aplicativos porque faz com que as <xref:Microsoft.EntityFrameworkCore.DbContext> instâncias sejam de longa duração e compartilhadas entre o aplicativo. <xref:Microsoft.EntityFrameworkCore.DbContext>Não é thread-safe e não deve ser usado simultaneamente.

Dependendo do aplicativo, o uso do <xref:Microsoft.AspNetCore.Components.OwningComponentBase> para limitar o escopo de um <xref:Microsoft.EntityFrameworkCore.DbContext> a um único componente *pode* resolver o problema. Se um componente não usa um <xref:Microsoft.EntityFrameworkCore.DbContext> em paralelo, derivar o componente de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> e recuperar o <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> é suficiente porque garante que:

* Componentes separados não compartilham um <xref:Microsoft.EntityFrameworkCore.DbContext> .
* O <xref:Microsoft.EntityFrameworkCore.DbContext> reside apenas desde que o componente dependa dele.

Se um único componente pode usar um <xref:Microsoft.EntityFrameworkCore.DbContext> simultaneamente (por exemplo, sempre que um usuário seleciona um botão), mesmo usando o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> não evita problemas com operações simultâneas do EF. Nesse caso, use outro <xref:Microsoft.EntityFrameworkCore.DbContext> para cada operação lógica do EF. Use uma das seguintes abordagens:

* Crie o <xref:Microsoft.EntityFrameworkCore.DbContext> usando diretamente <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> como um argumento, que pode ser recuperado de di e é thread-safe.

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

* Registre o <xref:Microsoft.EntityFrameworkCore.DbContext> no contêiner de serviço com um tempo de vida transitório:
  * Ao registrar o contexto, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . O <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> método de extensão usa dois parâmetros opcionais do tipo <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> . Para usar essa abordagem, somente o `contextLifetime` parâmetro precisa ser <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . `optionsLifetime`pode manter seu valor padrão de <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * O transitório <xref:Microsoft.EntityFrameworkCore.DbContext> pode ser injetado como normal (usando [`@inject`](xref:mvc/views/razor#inject) ) em componentes que não executarão várias operações de EF em paralelo. Os que podem executar várias operações de EF simultaneamente podem solicitar <xref:Microsoft.EntityFrameworkCore.DbContext> objetos separados para cada operação em paralelo usando <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .

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

## <a name="detect-transient-disposables"></a>Detectar descartáveis transitórios

Os exemplos a seguir mostram como detectar serviços descartáveis indetectáveis em um aplicativo que deve usar o <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Para obter mais informações, consulte a seção [classes de componente base do utilitário para gerenciar um escopo de di](#utility-base-component-classes-to-manage-a-di-scope) .

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

O `TransientDisposable` no exemplo a seguir é detectado ( `Program.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

O `TransientDependency` no exemplo a seguir é detectado ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/dependency-injection>
* [`IDisposable`Diretrizes para instâncias transitórias e compartilhadas](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
