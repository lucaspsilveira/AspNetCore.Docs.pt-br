---
<span data-ttu-id="55724-101">Título: ' ASP.NET Core Blazor modelo de hospedagem configuração ' autor: guardrex Descrição: ' saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC. '</span><span class="sxs-lookup"><span data-stu-id="55724-101">title: 'ASP.NET Core Blazor hosting model configuration' author: guardrex description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="55724-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/28/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="55724-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:</span></span>
- <span data-ttu-id="55724-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="55724-103">'Blazor'</span></span>
- <span data-ttu-id="55724-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="55724-104">'Identity'</span></span>
- <span data-ttu-id="55724-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="55724-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="55724-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="55724-106">'Razor'</span></span>
- <span data-ttu-id="55724-107">' SignalR ' UID: mais incrivelmente/de hospedagem-modelo-configuração</span><span class="sxs-lookup"><span data-stu-id="55724-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="55724-108">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="55724-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="55724-109">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="55724-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="55724-110">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="55724-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="55724-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="55724-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="55724-112">Ambiente</span><span class="sxs-lookup"><span data-stu-id="55724-112">Environment</span></span>

<span data-ttu-id="55724-113">Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="55724-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="55724-114">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="55724-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="55724-115">Um Blazor aplicativo Webassembly hospedado pega o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="55724-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="55724-116">O valor do cabeçalho é o ambiente.</span><span class="sxs-lookup"><span data-stu-id="55724-116">The value of the header is the environment.</span></span> <span data-ttu-id="55724-117">O Blazor aplicativo hospedado e o aplicativo de servidor compartilham o mesmo ambiente.</span><span class="sxs-lookup"><span data-stu-id="55724-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="55724-118">Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="55724-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="55724-119">Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="55724-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="55724-120">Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="55724-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="55724-121">No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao arquivo *Web. config* publicado.</span><span class="sxs-lookup"><span data-stu-id="55724-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="55724-122">O arquivo *Web. config* está localizado na pasta */versão/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="55724-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="55724-123">Para usar um arquivo *Web. config* personalizado para o IIS que não é substituído quando o aplicativo é publicado na pasta de *publicação* , consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="55724-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="55724-124">Obtenha o ambiente do aplicativo em um componente injetando <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e lendo a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="55724-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="55724-125">Durante a inicialização, o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expõe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> por meio da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:</span><span class="sxs-lookup"><span data-stu-id="55724-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="55724-126">Os métodos de extensão de conveniência a seguir permitem verificar o ambiente atual quanto a desenvolvimento, produção, preparação e nomes de ambiente personalizados:</span><span class="sxs-lookup"><span data-stu-id="55724-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="55724-127">A <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriedade pode ser usada durante a inicialização quando o <xref:Microsoft.AspNetCore.Components.NavigationManager> serviço não está disponível.</span><span class="sxs-lookup"><span data-stu-id="55724-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="55724-128">Configuração</span><span class="sxs-lookup"><span data-stu-id="55724-128">Configuration</span></span>

Blazor<span data-ttu-id="55724-129">O Webassembly carrega a configuração de:</span><span class="sxs-lookup"><span data-stu-id="55724-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="55724-130">Arquivos de configurações do aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="55724-130">App settings files by default:</span></span>
  * <span data-ttu-id="55724-131">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="55724-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="55724-132">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="55724-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="55724-133">Outros [provedores de configuração](xref:fundamentals/configuration/index) registrados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="55724-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="55724-134">Nem todos os provedores são apropriados para Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="55724-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="55724-135">O esclarecimento sobre quais provedores têm suporte para o Blazor Webassembly é acompanhado pelos [provedores de configuração do Clarify para Blazor WASM (dotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="55724-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="55724-136">A configuração em um Blazor aplicativo Webassembly é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="55724-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="55724-137">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="55724-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="55724-138">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="55724-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="55724-139">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="55724-139">App settings configuration</span></span>

<span data-ttu-id="55724-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55724-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="55724-141">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="55724-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="55724-142">Configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="55724-142">Provider configuration</span></span>

<span data-ttu-id="55724-143">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para fornecer configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="55724-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="55724-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="55724-144">`Program.Main`:</span></span>

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

<span data-ttu-id="55724-145">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="55724-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="55724-146">Para ler outros arquivos de configuração da pasta *wwwroot* para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="55724-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="55724-147">Ao usar essa abordagem, o <xref:System.Net.Http.HttpClient> registro de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="55724-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="55724-148">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55724-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="55724-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="55724-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="55724-150">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="55724-150">Authentication configuration</span></span>

<span data-ttu-id="55724-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55724-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="55724-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="55724-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="55724-153">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="55724-153">Logging configuration</span></span>

<span data-ttu-id="55724-154">Adicione uma referência de pacote para [Microsoft. Extensions. log. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="55724-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="55724-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55724-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="55724-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="55724-156">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="55724-157">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="55724-157">Host builder configuration</span></span>

<span data-ttu-id="55724-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="55724-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="55724-159">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="55724-159">Cached configuration</span></span>

<span data-ttu-id="55724-160">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="55724-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="55724-161">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="55724-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="55724-162">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="55724-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="55724-163">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="55724-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="55724-164">Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="55724-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="55724-165">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="55724-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="55724-166">Registro em log</span><span class="sxs-lookup"><span data-stu-id="55724-166">Logging</span></span>

<span data-ttu-id="55724-167">Para obter informações sobre o Blazor suporte a logs de Webassembly, consulte <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="55724-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="55724-168">Servidor</span><span class="sxs-lookup"><span data-stu-id="55724-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="55724-169">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="55724-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="55724-170">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="55724-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="55724-171">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="55724-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="55724-172">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da página *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="55724-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="55724-173">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="55724-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="55724-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="55724-174">CSS class</span></span>                       | <span data-ttu-id="55724-175">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="55724-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="55724-176">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="55724-176">A lost connection.</span></span> <span data-ttu-id="55724-177">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="55724-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="55724-178">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="55724-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="55724-179">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="55724-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="55724-180">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="55724-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="55724-181">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="55724-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="55724-182">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="55724-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="55724-183">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="55724-183">Reconnection rejected.</span></span> <span data-ttu-id="55724-184">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="55724-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="55724-185">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="55724-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="55724-186">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="55724-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="55724-187">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="55724-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="55724-188">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="55724-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="55724-189">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="55724-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="55724-190">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="55724-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="55724-191">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="55724-191">Render mode</span></span>

Blazor<span data-ttu-id="55724-192">Os aplicativos de servidor são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="55724-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="55724-193">Isso é configurado na página *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="55724-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="55724-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="55724-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="55724-195">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="55724-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="55724-196">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="55724-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="55724-197">Descrição</span><span class="sxs-lookup"><span data-stu-id="55724-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="55724-198">Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="55724-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="55724-199">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="55724-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="55724-200">Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="55724-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="55724-201">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="55724-201">Output from the component isn't included.</span></span> <span data-ttu-id="55724-202">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="55724-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="55724-203">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="55724-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="55724-204">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="55724-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="55724-205">Configurar o SignalR cliente para Blazor aplicativos de servidor</span><span class="sxs-lookup"><span data-stu-id="55724-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="55724-206">Às vezes, você precisa configurar o SignalR cliente usado por Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="55724-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="55724-207">Por exemplo, talvez você queira configurar o registro em log no SignalR cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="55724-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="55724-208">Para configurar o SignalR cliente no arquivo *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="55724-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="55724-209">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="55724-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="55724-210">Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.</span><span class="sxs-lookup"><span data-stu-id="55724-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="55724-211">Registro em log</span><span class="sxs-lookup"><span data-stu-id="55724-211">Logging</span></span>

<span data-ttu-id="55724-212">Para obter informações sobre o Blazor suporte a logs de servidor, consulte <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="55724-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
