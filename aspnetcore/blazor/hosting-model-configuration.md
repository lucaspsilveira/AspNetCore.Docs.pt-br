---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre Blazor a configuração do modelo de hospedagem, incluindo como integrar Razor componentes em Razor páginas e aplicativos MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: e3b8b91a570210e77f307c49f7be21eeab714daa
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84355104"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="cb1f7-103">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cb1f7-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="cb1f7-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb1f7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cb1f7-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="cb1f7-106">Webassembly</span><span class="sxs-lookup"><span data-stu-id="cb1f7-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="cb1f7-107">Ambiente</span><span class="sxs-lookup"><span data-stu-id="cb1f7-107">Environment</span></span>

<span data-ttu-id="cb1f7-108">Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="cb1f7-109">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="cb1f7-110">Um Blazor aplicativo Webassembly hospedado pega o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="cb1f7-111">O valor do cabeçalho é o ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-111">The value of the header is the environment.</span></span> <span data-ttu-id="cb1f7-112">O Blazor aplicativo hospedado e o aplicativo de servidor compartilham o mesmo ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="cb1f7-113">Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cb1f7-114">Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento adiciona o `blazor-environment` cabeçalho para especificar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="cb1f7-115">Para especificar o ambiente para outros ambientes de hospedagem, adicione o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="cb1f7-116">No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao arquivo *Web. config* publicado.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="cb1f7-117">O arquivo *Web. config* está localizado na pasta */versão/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="cb1f7-118">Para usar um arquivo *Web. config* personalizado para o IIS que não é substituído quando o aplicativo é publicado na pasta de *publicação* , consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="cb1f7-119">Obtenha o ambiente do aplicativo em um componente injetando <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e lendo a <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="cb1f7-120">Durante a inicialização, o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expõe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> por meio da <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="cb1f7-121">Os métodos de extensão de conveniência a seguir permitem verificar o ambiente atual quanto a desenvolvimento, produção, preparação e nomes de ambiente personalizados:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="cb1f7-122">A <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriedade pode ser usada durante a inicialização quando o <xref:Microsoft.AspNetCore.Components.NavigationManager> serviço não está disponível.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="cb1f7-123">Configuração</span><span class="sxs-lookup"><span data-stu-id="cb1f7-123">Configuration</span></span>

Blazor<span data-ttu-id="cb1f7-124">O Webassembly carrega a configuração de:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="cb1f7-125">Arquivos de configurações do aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-125">App settings files by default:</span></span>
  * <span data-ttu-id="cb1f7-126">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="cb1f7-127">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="cb1f7-128">Outros [provedores de configuração](xref:fundamentals/configuration/index) registrados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="cb1f7-129">Nem todos os provedores são apropriados para Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="cb1f7-130">O esclarecimento sobre quais provedores têm suporte para o Blazor Webassembly é acompanhado pelos [provedores de configuração do Clarify para Blazor WASM (dotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="cb1f7-131">A configuração em um Blazor aplicativo Webassembly é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="cb1f7-132">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="cb1f7-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="cb1f7-133">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="cb1f7-134">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="cb1f7-134">App settings configuration</span></span>

<span data-ttu-id="cb1f7-135">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="cb1f7-136">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="cb1f7-137">Configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="cb1f7-137">Provider configuration</span></span>

<span data-ttu-id="cb1f7-138">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para fornecer configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="cb1f7-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-139">`Program.Main`:</span></span>

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

<span data-ttu-id="cb1f7-140">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="cb1f7-141">Para ler outros arquivos de configuração da pasta *wwwroot* para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="cb1f7-142">Ao usar essa abordagem, o <xref:System.Net.Http.HttpClient> registro de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="cb1f7-143">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="cb1f7-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="cb1f7-145">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="cb1f7-145">Authentication configuration</span></span>

<span data-ttu-id="cb1f7-146">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="cb1f7-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="cb1f7-148">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="cb1f7-148">Logging configuration</span></span>

<span data-ttu-id="cb1f7-149">Adicione uma referência de pacote para [Microsoft. Extensions. log. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="cb1f7-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="cb1f7-150">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="cb1f7-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="cb1f7-152">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="cb1f7-152">Host builder configuration</span></span>

<span data-ttu-id="cb1f7-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="cb1f7-154">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="cb1f7-154">Cached configuration</span></span>

<span data-ttu-id="cb1f7-155">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="cb1f7-156">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="cb1f7-157">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="cb1f7-158">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="cb1f7-159">Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="cb1f7-160">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="cb1f7-161">Registro em log</span><span class="sxs-lookup"><span data-stu-id="cb1f7-161">Logging</span></span>

<span data-ttu-id="cb1f7-162">Para obter informações sobre o Blazor suporte a logs de Webassembly, consulte <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="cb1f7-163">Servidor</span><span class="sxs-lookup"><span data-stu-id="cb1f7-163"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="cb1f7-164">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="cb1f7-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="cb1f7-165">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="cb1f7-166">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="cb1f7-167">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da página *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="cb1f7-168">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="cb1f7-169">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="cb1f7-169">CSS class</span></span>                       | <span data-ttu-id="cb1f7-170">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="cb1f7-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="cb1f7-171">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-171">A lost connection.</span></span> <span data-ttu-id="cb1f7-172">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="cb1f7-173">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="cb1f7-174">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="cb1f7-175">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="cb1f7-176">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="cb1f7-177">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="cb1f7-178">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-178">Reconnection rejected.</span></span> <span data-ttu-id="cb1f7-179">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="cb1f7-180">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="cb1f7-181">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="cb1f7-182">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="cb1f7-183">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="cb1f7-184">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="cb1f7-185">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="cb1f7-186">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="cb1f7-186">Render mode</span></span>

Blazor<span data-ttu-id="cb1f7-187">Os aplicativos de servidor são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-187"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="cb1f7-188">Isso é configurado na página *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="cb1f7-189"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-189"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="cb1f7-190">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="cb1f7-191">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="cb1f7-192">Descrição</span><span class="sxs-lookup"><span data-stu-id="cb1f7-192">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cb1f7-193">Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cb1f7-194">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cb1f7-195">Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cb1f7-196">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-196">Output from the component isn't included.</span></span> <span data-ttu-id="cb1f7-197">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cb1f7-198">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="cb1f7-199">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="cb1f7-200">Configurar o SignalR cliente para Blazor aplicativos de servidor</span><span class="sxs-lookup"><span data-stu-id="cb1f7-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="cb1f7-201">Às vezes, você precisa configurar o SignalR cliente usado por Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="cb1f7-202">Por exemplo, talvez você queira configurar o registro em log no SignalR cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="cb1f7-203">Para configurar o SignalR cliente no arquivo *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="cb1f7-204">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="cb1f7-205">Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="cb1f7-206">Registro em log</span><span class="sxs-lookup"><span data-stu-id="cb1f7-206">Logging</span></span>

<span data-ttu-id="cb1f7-207">Para obter informações sobre o Blazor suporte a logs de servidor, consulte <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
