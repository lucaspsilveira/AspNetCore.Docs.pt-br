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
# <a name="aspnet-core-blazor-dependency-injection"></a>injeção de dependência de ASP.NET Core Blazor

Por [Rainer Stropek](https://www.timecockpit.com) e [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor suporta [injeção de dependência (DI)](xref:fundamentals/dependency-injection). Os aplicativos podem usar serviços incorporados injetando-os em componentes. Os aplicativos também podem definir e registrar serviços personalizados e disponibilizá-los em todo o aplicativo via DI.

DI é uma técnica de acesso a serviços configurados em um local central. Isso pode ser útil em aplicativos Blazor para:

* Compartilhe uma única instância de uma classe de serviço em muitos componentes, conhecido como serviço *singleton.*
* Desacoplar componentes de classes de serviço de concreto usando abstrações de referência. Por exemplo, considere `IDataAccess` uma interface para acessar dados no aplicativo. A interface é implementada `DataAccess` por uma classe de concreto e registrada como um serviço no contêiner de serviço do aplicativo. Quando um componente usa `IDataAccess` DI para receber uma implementação, o componente não é acoplado ao tipo de concreto. A implementação pode ser trocada, talvez para uma implementação simulada em testes unitários.

## <a name="default-services"></a>Serviços padrão

Os serviços padrão são adicionados automaticamente à coleção de serviços do aplicativo.

| Serviço | Tempo de vida | Descrição |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Fornece métodos para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por um URI.<br><br>A instância `HttpClient` de um aplicativo Blazor WebAssembly usa o navegador para lidar com o tráfego HTTP em segundo plano.<br><br>Os aplicativos do Blazor `HttpClient` Server não incluem um serviço configurado como um serviço por padrão. Forneça `HttpClient` um aplicativo blazor server.<br><br>Para obter mais informações, consulte <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Escopo (Servidor Blazor) | Representa uma instância de um tempo de execução JavaScript onde as chamadas JavaScript são enviadas. Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Escopo (Servidor Blazor) | Contém ajudantes para trabalhar com URIs e estado de navegação. Para obter mais informações, consulte [uri e ajudantes de estado de navegação](xref:blazor/routing#uri-and-navigation-state-helpers). |

Um provedor de serviços personalizado não fornece automaticamente os serviços padrão listados na tabela. Se você usar um provedor de serviços personalizado e exigir qualquer um dos serviços mostrados na tabela, adicione os serviços necessários ao novo provedor de serviços.

## <a name="add-services-to-an-app"></a>Adicionar serviços a um aplicativo

### <a name="blazor-webassembly"></a>WebAssembly Blazor

Configure serviços para a coleta `Main` de serviços do aplicativo no método de *Program.cs*. No exemplo a `MyDependency` seguir, a `IMyDependency`implementação é registrada para :

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

Uma vez que o host é construído, os serviços podem ser acessados a partir do escopo DI raiz antes que quaisquer componentes sejam renderizados. Isso pode ser útil para executar a lógica de inicialização antes de renderizar o conteúdo:

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

O host também fornece uma instância de configuração central para o aplicativo. Com base no exemplo anterior, a URL do serviço meteorológico é passada de uma fonte de `InitializeWeatherAsync`configuração padrão (por exemplo, *appsettings.json)* para :

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

### <a name="blazor-server"></a>Servidor Blazor

Depois de criar um `Startup.ConfigureServices` novo aplicativo, examine o método:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

O `ConfigureServices` método é <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>aprovado um , que é uma<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>lista de objetos descritores de serviço ( ). Os serviços são adicionados fornecendo descritores de serviço à coleta de serviços. O exemplo a seguir `IDataAccess` demonstra o conceito `DataAccess`com a interface e sua implementação concreta:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Vida útil do serviço

Os serviços podem ser configurados com as vidas de vida mostradas na tabela a seguir.

| Tempo de vida | Descrição |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorOs aplicativos WebAssembly não têm atualmente um conceito de escopos DI. `Scoped`- serviços registrados `Singleton` se comportam como serviços. No entanto, o Blazor modelo `Scoped` de hospedagem do Servidor suporta a vida útil. Nos Blazor aplicativos server, um registro de serviço escopo é escopo para a *conexão*. Por essa razão, o uso de serviços escopo é preferido para serviços que devem ser escopo para o usuário atual, mesmo que a intenção atual seja executar o lado do cliente no navegador. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | O DI cria uma *única instância* do serviço. Todos os componentes que requerem um `Singleton` serviço recebem uma instância do mesmo serviço. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Sempre que um componente obtém uma instância de um `Transient` serviço do contêiner de serviço, ele recebe uma nova *instância* do serviço. |

O sistema DI é baseado no sistema DI em ASP.NET Core. Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Solicite um serviço em um componente

Após a adição dos serviços à coleta de [ \@](xref:mvc/views/razor#inject) serviços, injete os serviços nos componentes usando a diretiva Razor inject. `@inject`tem dois parâmetros:

* Digite &ndash; o tipo de serviço a injetar.
* Propriedade &ndash; O nome da propriedade que recebe o serviço de aplicativo injetado. A propriedade não requer criação manual. O compilador cria a propriedade.

Para obter mais informações, consulte <xref:mvc/views/dependency-injection>.

Use `@inject` várias instruções para injetar diferentes serviços.

O exemplo a seguir mostra como usar `@inject`. A implementação `Services.IDataAccess` do serviço é injetada `DataRepository`na propriedade do componente. Observe como o código `IDataAccess` está usando apenas a abstração:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Internamente, a propriedade`DataRepository`gerada `InjectAttribute` ( ) usa o atributo. Normalmente, esse atributo não é usado diretamente. Se uma classe base for necessária para componentes e propriedades injetadas também `InjectAttribute`forem necessárias para a classe base, adicione manualmente:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Em componentes derivados da classe `@inject` base, a diretiva não é necessária. A `InjectAttribute` classe base é suficiente:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Use DI em serviços

Serviços complexos podem exigir serviços adicionais. No exemplo anterior, `DataAccess` pode `HttpClient` exigir o serviço padrão. `@inject`(ou `InjectAttribute`o ) não está disponível para uso em serviços. *Em* vez disso, deve-se usar a injeção de construção. Os serviços necessários são adicionados adicionando parâmetros ao construtor do serviço. Quando a DI cria o serviço, reconhece os serviços que necessita na construtora e os fornece de acordo.

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

Pré-requisitos para injeção de construtores:

* Um construtor deve existir cujos argumentos podem ser todos cumpridos pela DI. Parâmetros adicionais não cobertos pelo DI são permitidos se especificarem valores padrão.
* O construtor aplicável deve ser *público.*
* Um construtor aplicável deve existir. Em caso de ambiguidade, di lança uma exceção.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de componentes de base de utilidade para gerenciar um escopo DI

Em ASP.NET de aplicativos Core, os serviços escopo são tipicamente escopo para a solicitação atual. Após a solicitação ser concluída, quaisquer serviços escopo ou transitórios são descartados pelo sistema DI. Nos Blazor aplicativos server, o escopo de solicitação dura a duração da conexão do cliente, o que pode resultar em serviços transitórios e escopo vivendo muito mais do que o esperado. Nos Blazor aplicativos WebAssembly, os serviços registrados com uma vida útil escopo são tratados como singletons, de modo que eles vivem mais do que serviços escopo em aplicativos típicos ASP.NET Core.

Uma abordagem que limita Blazor uma vida `OwningComponentBase` útil em aplicativos é o uso do tipo. `OwningComponentBase`é um tipo abstrato derivado `ComponentBase` de que cria um escopo DI correspondente à vida útil do componente. Usando este escopo, é possível usar serviços DI com uma vida útil escopo e fazê-los viver enquanto o componente. Quando o componente é destruído, os serviços do provedor de serviços escopo do componente também são descartados. Isso pode ser útil para serviços que:

* Deve ser reutilizado dentro de um componente, pois a vida útil transitória é inadequada.
* Não deve ser compartilhado entre componentes, pois a vida de singleton é inadequada.

Duas versões do `OwningComponentBase` tipo estão disponíveis:

* `OwningComponentBase`é uma criança abstrata `ComponentBase` e descartável `ScopedServices` do `IServiceProvider`tipo com uma propriedade protegida do tipo. Este provedor pode ser usado para resolver serviços que são escopo para a vida útil do componente.

  Os serviços DI injetados `InjectAttribute` no`[Inject]`componente usando `@inject` ou () não são criados no escopo do componente. Para usar o escopo do componente, `ScopedServices.GetRequiredService` os `ScopedServices.GetService`serviços devem ser resolvidos usando ou . Todos os serviços resolvidos usando o `ScopedServices` provedor têm suas dependências fornecidas a partir desse mesmo escopo.

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

* `OwningComponentBase<T>`deriva `OwningComponentBase` e adiciona uma `Service` propriedade que `T` retorna uma instância do provedor DI escopo. Esse tipo é uma maneira conveniente de acessar `IServiceProvider` serviços com escopo sem usar uma instância de quando há um serviço principal que o aplicativo requer do contêiner DI usando o escopo do componente. A `ScopedServices` propriedade está disponível, para que o aplicativo possa obter serviços de outros tipos, se necessário.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Uso do Entity Framework DbContext da DI

Um tipo de serviço comum para recuperar do DI `DbContext` em aplicativos web são objetos Entity Framework (EF). Registrar serviços EF `IServiceCollection.AddDbContext` `DbContext` usando adiciona o como um serviço escopo por padrão. Registrar-se como um serviço escopo Blazor pode levar `DbContext` a problemas em aplicativos porque faz com que os casos sejam de longa duração e compartilhados em todo o aplicativo. `DbContext`não é seguro para rosca e não deve ser usado simultaneamente.

Dependendo do aplicativo, `OwningComponentBase` usar para limitar `DbContext` o escopo de a a um único componente *pode* resolver o problema. Se um componente não `DbContext` usar um em paralelo, `OwningComponentBase` derivar `DbContext` o `ScopedServices` componente e recuperar o de é suficiente porque garante que:

* Componentes separados não `DbContext`compartilham um .
* As `DbContext` vidas só podem ser enquanto o componente depender dele.

Se um único componente `DbContext` pode usar um simultaneamente (por exemplo, toda `OwningComponentBase` vez que um usuário seleciona um botão), mesmo o uso não evita problemas com operações EF simultâneas. Nesse caso, use `DbContext` um diferente para cada operação lógica de EF. Use qualquer uma das seguintes abordagens:

* Crie `DbContext` o `DbContextOptions<TContext>` uso direto como argumento, que pode ser recuperado de DI e é seguro para rosca.

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

* Registre `DbContext` o recipiente no serviço com uma vida útil transitória:
  * Ao registrar o contexto, use `ServiceLifetime.Transient`. O `AddDbContext` método de extensão `ServiceLifetime`leva dois parâmetros opcionais do tipo . Para usar esta abordagem, apenas `contextLifetime` `ServiceLifetime.Transient`o parâmetro precisa ser . `optionsLifetime`pode manter seu `ServiceLifetime.Scoped`valor padrão de .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * O transitório `DbContext` pode ser injetado `@inject`normalmente (usando ) em componentes que não executarão várias operações EF em paralelo. Aqueles que podem realizar várias operações EF simultaneamente podem solicitar objetos separados `DbContext` para cada operação paralela usando `IServiceProvider.GetRequiredService`.

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
