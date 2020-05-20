---
<span data-ttu-id="c569d-101">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-103">'Blazor'</span></span>
- <span data-ttu-id="c569d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-104">'Identity'</span></span>
- <span data-ttu-id="c569d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-106">'Razor'</span></span>
- <span data-ttu-id="c569d-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="c569d-108">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c569d-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="c569d-109">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c569d-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c569d-110">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c569d-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="c569d-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="c569d-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="c569d-112">Ambiente</span><span class="sxs-lookup"><span data-stu-id="c569d-112">Environment</span></span>

<span data-ttu-id="c569d-113">Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c569d-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="c569d-114">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="c569d-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="c569d-115">Um Blazor aplicativo Webassembly hospedado pega o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="c569d-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="c569d-116">O valor do cabeçalho é o ambiente.</span><span class="sxs-lookup"><span data-stu-id="c569d-116">The value of the header is the environment.</span></span> <span data-ttu-id="c569d-117">O Blazor aplicativo hospedado e o aplicativo de servidor compartilham o mesmo ambiente.</span><span class="sxs-lookup"><span data-stu-id="c569d-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="c569d-118">Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="c569d-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="c569d-119">Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c569d-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="c569d-120">Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="c569d-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="c569d-121">No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao arquivo *Web. config* publicado.</span><span class="sxs-lookup"><span data-stu-id="c569d-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="c569d-122">O arquivo *Web. config* está localizado na pasta */versão/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="c569d-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="c569d-123">Para usar um arquivo *Web. config* personalizado para o IIS que não é substituído quando o aplicativo é publicado na pasta de *publicação* , consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="c569d-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="c569d-124">Obtenha o ambiente do aplicativo em um componente injetando `IWebAssemblyHostEnvironment` e lendo a `Environment` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="c569d-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="c569d-125">Durante a inicialização, o `WebAssemblyHostBuilder` expõe o `IWebAssemblyHostEnvironment` por meio da `HostEnvironment` propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:</span><span class="sxs-lookup"><span data-stu-id="c569d-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="c569d-126">Os métodos de extensão de conveniência a seguir permitem verificar o ambiente atual quanto a desenvolvimento, produção, preparação e nomes de ambiente personalizados:</span><span class="sxs-lookup"><span data-stu-id="c569d-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="c569d-127">' Isenvironment ("{nome do ambiente}")</span><span class="sxs-lookup"><span data-stu-id="c569d-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="c569d-128">A `IWebAssemblyHostEnvironment.BaseAddress` propriedade pode ser usada durante a inicialização quando o `NavigationManager` serviço não está disponível.</span><span class="sxs-lookup"><span data-stu-id="c569d-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="c569d-129">Configuração</span><span class="sxs-lookup"><span data-stu-id="c569d-129">Configuration</span></span>

Blazor<span data-ttu-id="c569d-130">O Webassembly carrega a configuração de:</span><span class="sxs-lookup"><span data-stu-id="c569d-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="c569d-131">Arquivos de configurações do aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="c569d-131">App settings files by default:</span></span>
  * <span data-ttu-id="c569d-132">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="c569d-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="c569d-133">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="c569d-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="c569d-134">Outros [provedores de configuração](xref:fundamentals/configuration/index) registrados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c569d-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="c569d-135">Nem todos os provedores são apropriados para Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="c569d-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="c569d-136">O esclarecimento sobre quais provedores têm suporte para o Blazor Webassembly é acompanhado pelos [provedores de configuração do Clarify para Blazor WASM (dotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="c569d-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="c569d-137">A configuração em um Blazor aplicativo Webassembly é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="c569d-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="c569d-138">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="c569d-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="c569d-139">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="c569d-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="c569d-140">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="c569d-140">App settings configuration</span></span>

<span data-ttu-id="c569d-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="c569d-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="c569d-142">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="c569d-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="c569d-143">Configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="c569d-143">Provider configuration</span></span>

<span data-ttu-id="c569d-144">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para fornecer configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="c569d-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="c569d-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c569d-145">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="c569d-146">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="c569d-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="c569d-147">Para ler outros arquivos de configuração da pasta *wwwroot* para a configuração, use um `HttpClient` para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c569d-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="c569d-148">Ao usar essa abordagem, o `HttpClient` registro de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="c569d-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="c569d-149">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="c569d-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="c569d-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c569d-150">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="c569d-151">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="c569d-151">Authentication configuration</span></span>

<span data-ttu-id="c569d-152">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="c569d-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="c569d-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c569d-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="c569d-154">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="c569d-154">Logging configuration</span></span>

<span data-ttu-id="c569d-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="c569d-155">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="c569d-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c569d-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="c569d-157">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="c569d-157">Host builder configuration</span></span>

<span data-ttu-id="c569d-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c569d-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="c569d-159">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="c569d-159">Cached configuration</span></span>

<span data-ttu-id="c569d-160">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="c569d-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="c569d-161">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="c569d-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="c569d-162">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="c569d-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="c569d-163">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="c569d-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="c569d-164">Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="c569d-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="c569d-165">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="c569d-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="c569d-166">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="c569d-166">Logging</span></span>

<span data-ttu-id="c569d-167">Para obter informações sobre o Blazor suporte a logs de Webassembly, consulte <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="c569d-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="c569d-168">Servidor</span><span class="sxs-lookup"><span data-stu-id="c569d-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c569d-169">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="c569d-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c569d-170">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="c569d-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c569d-171">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="c569d-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c569d-172">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da página *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="c569d-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c569d-173">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="c569d-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="c569d-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="c569d-174">CSS class</span></span>                       | <span data-ttu-id="c569d-175">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="c569d-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="c569d-176">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-177">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-178">'Blazor'</span></span>
- <span data-ttu-id="c569d-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-179">'Identity'</span></span>
- <span data-ttu-id="c569d-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-181">'Razor'</span></span>
- <span data-ttu-id="c569d-182">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-183">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-184">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-185">'Blazor'</span></span>
- <span data-ttu-id="c569d-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-186">'Identity'</span></span>
- <span data-ttu-id="c569d-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-188">'Razor'</span></span>
- <span data-ttu-id="c569d-189">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-190">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-191">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-192">'Blazor'</span></span>
- <span data-ttu-id="c569d-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-193">'Identity'</span></span>
- <span data-ttu-id="c569d-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-195">'Razor'</span></span>
- <span data-ttu-id="c569d-196">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-197">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-198">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-199">'Blazor'</span></span>
- <span data-ttu-id="c569d-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-200">'Identity'</span></span>
- <span data-ttu-id="c569d-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-202">'Razor'</span></span>
- <span data-ttu-id="c569d-203">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-204">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-205">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-206">'Blazor'</span></span>
- <span data-ttu-id="c569d-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-207">'Identity'</span></span>
- <span data-ttu-id="c569d-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-209">'Razor'</span></span>
- <span data-ttu-id="c569d-210">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-211">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-212">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-213">'Blazor'</span></span>
- <span data-ttu-id="c569d-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-214">'Identity'</span></span>
- <span data-ttu-id="c569d-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-216">'Razor'</span></span>
- <span data-ttu-id="c569d-217">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-218">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-219">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-220">'Blazor'</span></span>
- <span data-ttu-id="c569d-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-221">'Identity'</span></span>
- <span data-ttu-id="c569d-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-223">'Razor'</span></span>
- <span data-ttu-id="c569d-224">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-225">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-226">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-227">'Blazor'</span></span>
- <span data-ttu-id="c569d-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-228">'Identity'</span></span>
- <span data-ttu-id="c569d-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-230">'Razor'</span></span>
- <span data-ttu-id="c569d-231">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-232">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-233">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-234">'Blazor'</span></span>
- <span data-ttu-id="c569d-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-235">'Identity'</span></span>
- <span data-ttu-id="c569d-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-237">'Razor'</span></span>
- <span data-ttu-id="c569d-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-239">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-240">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-241">'Blazor'</span></span>
- <span data-ttu-id="c569d-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-242">'Identity'</span></span>
- <span data-ttu-id="c569d-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-244">'Razor'</span></span>
- <span data-ttu-id="c569d-245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-246">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-247">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-248">'Blazor'</span></span>
- <span data-ttu-id="c569d-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-249">'Identity'</span></span>
- <span data-ttu-id="c569d-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-251">'Razor'</span></span>
- <span data-ttu-id="c569d-252">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-253">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-254">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-255">'Blazor'</span></span>
- <span data-ttu-id="c569d-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-256">'Identity'</span></span>
- <span data-ttu-id="c569d-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-258">'Razor'</span></span>
- <span data-ttu-id="c569d-259">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-260">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-261">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-262">'Blazor'</span></span>
- <span data-ttu-id="c569d-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-263">'Identity'</span></span>
- <span data-ttu-id="c569d-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-265">'Razor'</span></span>
- <span data-ttu-id="c569d-266">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-266">'SignalR' uid:</span></span> 

<span data-ttu-id="c569d-267">---------------- | título do---: ' ASP.NET Core Blazor configuração do modelo de hospedagem ' ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-268">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-269">'Blazor'</span></span>
- <span data-ttu-id="c569d-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-270">'Identity'</span></span>
- <span data-ttu-id="c569d-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-272">'Razor'</span></span>
- <span data-ttu-id="c569d-273">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-274">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-275">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-276">'Blazor'</span></span>
- <span data-ttu-id="c569d-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-277">'Identity'</span></span>
- <span data-ttu-id="c569d-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-279">'Razor'</span></span>
- <span data-ttu-id="c569d-280">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-281">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-282">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-283">'Blazor'</span></span>
- <span data-ttu-id="c569d-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-284">'Identity'</span></span>
- <span data-ttu-id="c569d-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-286">'Razor'</span></span>
- <span data-ttu-id="c569d-287">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-288">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-289">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-290">'Blazor'</span></span>
- <span data-ttu-id="c569d-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-291">'Identity'</span></span>
- <span data-ttu-id="c569d-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-293">'Razor'</span></span>
- <span data-ttu-id="c569d-294">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-295">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-296">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-297">'Blazor'</span></span>
- <span data-ttu-id="c569d-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-298">'Identity'</span></span>
- <span data-ttu-id="c569d-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-300">'Razor'</span></span>
- <span data-ttu-id="c569d-301">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-302">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-303">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-304">'Blazor'</span></span>
- <span data-ttu-id="c569d-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-305">'Identity'</span></span>
- <span data-ttu-id="c569d-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-307">'Razor'</span></span>
- <span data-ttu-id="c569d-308">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-308">'SignalR' uid:</span></span> 

<span data-ttu-id="c569d-309">--------- | | `components-reconnect-show`     | Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="c569d-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="c569d-310">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="c569d-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="c569d-311">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="c569d-311">Show the modal.</span></span> <span data-ttu-id="c569d-312">| | `components-reconnect-hide`     | Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="c569d-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="c569d-313">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="c569d-313">Hide the modal.</span></span> <span data-ttu-id="c569d-314">| | `components-reconnect-failed`   | Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="c569d-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c569d-315">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="c569d-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="c569d-316">| | `components-reconnect-rejected` | Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="c569d-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="c569d-317">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="c569d-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c569d-318">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="c569d-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="c569d-319">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="c569d-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="c569d-320">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="c569d-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c569d-321">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="c569d-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c569d-322">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="c569d-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="c569d-323">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="c569d-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="c569d-324">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="c569d-324">Render mode</span></span>

Blazor<span data-ttu-id="c569d-325">Os aplicativos de servidor são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="c569d-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c569d-326">Isso é configurado na página *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="c569d-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="c569d-327">`RenderMode`configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="c569d-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="c569d-328">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="c569d-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="c569d-329">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="c569d-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="c569d-330">Descrição</span><span class="sxs-lookup"><span data-stu-id="c569d-330">Description</span></span> |
| ---
<span data-ttu-id="c569d-331">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-332">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-333">'Blazor'</span></span>
- <span data-ttu-id="c569d-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-334">'Identity'</span></span>
- <span data-ttu-id="c569d-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-336">'Razor'</span></span>
- <span data-ttu-id="c569d-337">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-338">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-339">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-340">'Blazor'</span></span>
- <span data-ttu-id="c569d-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-341">'Identity'</span></span>
- <span data-ttu-id="c569d-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-343">'Razor'</span></span>
- <span data-ttu-id="c569d-344">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-345">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-346">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-347">'Blazor'</span></span>
- <span data-ttu-id="c569d-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-348">'Identity'</span></span>
- <span data-ttu-id="c569d-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-350">'Razor'</span></span>
- <span data-ttu-id="c569d-351">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-352">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-353">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-354">'Blazor'</span></span>
- <span data-ttu-id="c569d-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-355">'Identity'</span></span>
- <span data-ttu-id="c569d-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-357">'Razor'</span></span>
- <span data-ttu-id="c569d-358">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-359">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-360">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-361">'Blazor'</span></span>
- <span data-ttu-id="c569d-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-362">'Identity'</span></span>
- <span data-ttu-id="c569d-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-364">'Razor'</span></span>
- <span data-ttu-id="c569d-365">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-366">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-367">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-368">'Blazor'</span></span>
- <span data-ttu-id="c569d-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-369">'Identity'</span></span>
- <span data-ttu-id="c569d-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-371">'Razor'</span></span>
- <span data-ttu-id="c569d-372">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-373">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-374">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-375">'Blazor'</span></span>
- <span data-ttu-id="c569d-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-376">'Identity'</span></span>
- <span data-ttu-id="c569d-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-378">'Razor'</span></span>
- <span data-ttu-id="c569d-379">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-379">'SignalR' uid:</span></span> 

<span data-ttu-id="c569d-380">---------- | título do---: ' ASP.NET Core Blazor configuração do modelo de hospedagem ' ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-381">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-382">'Blazor'</span></span>
- <span data-ttu-id="c569d-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-383">'Identity'</span></span>
- <span data-ttu-id="c569d-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-385">'Razor'</span></span>
- <span data-ttu-id="c569d-386">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-387">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-388">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-389">'Blazor'</span></span>
- <span data-ttu-id="c569d-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-390">'Identity'</span></span>
- <span data-ttu-id="c569d-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-392">'Razor'</span></span>
- <span data-ttu-id="c569d-393">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c569d-394">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="c569d-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="c569d-395">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c569d-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c569d-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c569d-396">'Blazor'</span></span>
- <span data-ttu-id="c569d-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c569d-397">'Identity'</span></span>
- <span data-ttu-id="c569d-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c569d-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="c569d-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c569d-399">'Razor'</span></span>
- <span data-ttu-id="c569d-400">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="c569d-400">'SignalR' uid:</span></span> 

<span data-ttu-id="c569d-401">------ | | `ServerPrerendered` | Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="c569d-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c569d-402">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c569d-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c569d-403">| | `Server`            | Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="c569d-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c569d-404">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="c569d-404">Output from the component isn't included.</span></span> <span data-ttu-id="c569d-405">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c569d-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c569d-406">| | `Static`            | Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="c569d-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="c569d-407">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="c569d-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="c569d-408">Configurar o SignalR cliente para Blazor aplicativos de servidor</span><span class="sxs-lookup"><span data-stu-id="c569d-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="c569d-409">Às vezes, você precisa configurar o SignalR cliente usado por Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="c569d-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="c569d-410">Por exemplo, talvez você queira configurar o registro em log no SignalR cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="c569d-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="c569d-411">Para configurar o SignalR cliente no arquivo *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c569d-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="c569d-412">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="c569d-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c569d-413">Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.</span><span class="sxs-lookup"><span data-stu-id="c569d-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="c569d-414">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="c569d-414">Logging</span></span>

<span data-ttu-id="c569d-415">Para obter informações sobre o Blazor suporte a logs de servidor, consulte <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="c569d-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
