---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais Blazor sobre a configuração do modelo de hospedagem, incluindo como integrar componentes do Razor em aplicativos Razor Pages e MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159613"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="0ffd4-103">ASP.NET Core configuração de modelo de hospedagem mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="0ffd4-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="0ffd4-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0ffd4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0ffd4-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="0ffd4-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="0ffd4-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="0ffd4-107">Ambiente</span><span class="sxs-lookup"><span data-stu-id="0ffd4-107">Environment</span></span>

<span data-ttu-id="0ffd4-108">Ao executar um aplicativo localmente, o ambiente assume como padrão o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="0ffd4-109">Quando o aplicativo é publicado, o ambiente assume como padrão a produção.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="0ffd4-110">Um aplicativo Webassembly de mais de um e-app hospedado escolhe o ambiente do servidor por meio de um middleware que comunica o ambiente ao navegador adicionando o `blazor-environment` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="0ffd4-111">O valor do cabeçalho é o ambiente.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-111">The value of the header is the environment.</span></span> <span data-ttu-id="0ffd4-112">O aplicativo e o aplicativo de servidor hospedados compartilham o mesmo ambiente.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="0ffd4-113">Para obter mais informações, incluindo como configurar o ambiente, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0ffd4-114">Para um aplicativo autônomo em execução localmente, o servidor de desenvolvimento `blazor-environment` adiciona o cabeçalho para especificar o ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="0ffd4-115">Para especificar o ambiente para outros ambientes de hospedagem, adicione `blazor-environment` o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="0ffd4-116">No exemplo a seguir para o IIS, adicione o cabeçalho personalizado ao arquivo *Web. config* publicado.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="0ffd4-117">O arquivo *Web. config* está localizado na pasta */versão/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="0ffd4-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="0ffd4-118">Para usar um arquivo *Web. config* personalizado para o IIS que não é substituído quando o aplicativo é publicado na pasta de *publicação* , <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>consulte.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="0ffd4-119">Obtenha o ambiente do aplicativo em um componente injetando `IWebAssemblyHostEnvironment` e lendo a `Environment` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="0ffd4-120">Durante a inicialização, `WebAssemblyHostBuilder` o expõe `IWebAssemblyHostEnvironment` o por `HostEnvironment` meio da propriedade, o que permite aos desenvolvedores ter lógica específica do ambiente em seu código:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="0ffd4-121">Os métodos de extensão de conveniência a seguir permitem verificar o ambiente atual quanto a desenvolvimento, produção, preparação e nomes de ambiente personalizados:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="0ffd4-122">' Isenvironment ("{nome do ambiente}")</span><span class="sxs-lookup"><span data-stu-id="0ffd4-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="0ffd4-123">A `IWebAssemblyHostEnvironment.BaseAddress` propriedade pode ser usada durante a inicialização quando `NavigationManager` o serviço não está disponível.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="0ffd4-124">Configuração</span><span class="sxs-lookup"><span data-stu-id="0ffd4-124">Configuration</span></span>

<span data-ttu-id="0ffd4-125">Webassembly mais incrivelmente dá suporte à configuração de:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="0ffd4-126">O [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) para arquivos de configurações de aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="0ffd4-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="0ffd4-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="0ffd4-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="0ffd4-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="0ffd4-129">Outros [provedores de configuração](xref:fundamentals/configuration/index) registrados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="0ffd4-130">A configuração em um aplicativo Webassembly mais incrivelmente é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="0ffd4-131">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="0ffd4-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="0ffd4-132">Para obter mais informações sobre provedores de configuração <xref:fundamentals/configuration/index>, consulte.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="0ffd4-133">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0ffd4-133">App settings configuration</span></span>

<span data-ttu-id="0ffd4-134">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="0ffd4-135">Injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> uma instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="0ffd4-136">Configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="0ffd4-136">Provider configuration</span></span>

<span data-ttu-id="0ffd4-137">O exemplo a seguir usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> um e o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) para fornecer configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="0ffd4-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

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

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="0ffd4-139">Injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> uma instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="0ffd4-140">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="0ffd4-140">Authentication configuration</span></span>

<span data-ttu-id="0ffd4-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="0ffd4-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="0ffd4-143">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="0ffd4-143">Logging configuration</span></span>

<span data-ttu-id="0ffd4-144">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="0ffd4-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="0ffd4-146">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="0ffd4-146">Host builder configuration</span></span>

<span data-ttu-id="0ffd4-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="0ffd4-148">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="0ffd4-148">Cached configuration</span></span>

<span data-ttu-id="0ffd4-149">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="0ffd4-150">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="0ffd4-151">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="0ffd4-152">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="0ffd4-153">Os arquivos *Service-Worker. js* e *Service-Worker-assets. js* do PWA devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="0ffd4-154">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="0ffd4-155">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="0ffd4-155">Logging</span></span>

<span data-ttu-id="0ffd4-156">Para obter informações sobre o suporte ao log de Webassembly <xref:fundamentals/logging/index#create-logs-in-blazor>mais incrivelmente, consulte.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="0ffd4-157">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="0ffd4-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="0ffd4-158">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="0ffd4-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="0ffd4-159">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="0ffd4-160">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="0ffd4-161">Para personalizar a interface do usuário, defina um elemento `id` com `components-reconnect-modal` um de `<body>` no da página do Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0ffd4-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="0ffd4-162">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="0ffd4-163">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="0ffd4-163">CSS class</span></span>                       | <span data-ttu-id="0ffd4-164">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="0ffd4-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="0ffd4-165">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-165">A lost connection.</span></span> <span data-ttu-id="0ffd4-166">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="0ffd4-167">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="0ffd4-168">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="0ffd4-169">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="0ffd4-170">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="0ffd4-171">Para tentar a reconexão, `window.Blazor.reconnect()`chame.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="0ffd4-172">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-172">Reconnection rejected.</span></span> <span data-ttu-id="0ffd4-173">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="0ffd4-174">Para recarregar o aplicativo, chame `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="0ffd4-175">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="0ffd4-176">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="0ffd4-177">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="0ffd4-178">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="0ffd4-179">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="0ffd4-180">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="0ffd4-180">Render mode</span></span>

<span data-ttu-id="0ffd4-181">Os aplicativos de servidor mais poseriais são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="0ffd4-182">Isso é configurado na página Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0ffd4-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="0ffd4-183">`RenderMode`configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="0ffd4-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="0ffd4-184">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="0ffd4-185">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="0ffd4-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="0ffd4-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="0ffd4-187">Renderiza o componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="0ffd4-188">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="0ffd4-189">Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="0ffd4-190">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-190">Output from the component isn't included.</span></span> <span data-ttu-id="0ffd4-191">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="0ffd4-192">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="0ffd4-193">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="0ffd4-194">Configurar o SignalR cliente para Blazor aplicativos de servidor</span><span class="sxs-lookup"><span data-stu-id="0ffd4-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="0ffd4-195">Às vezes, você precisa configurar o SignalR cliente usado por Blazor aplicativos de servidor.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="0ffd4-196">Por exemplo, talvez você queira configurar o SignalR registro em log no cliente para diagnosticar um problema de conexão.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="0ffd4-197">Para configurar o SignalR cliente no arquivo *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0ffd4-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="0ffd4-198">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="0ffd4-199">Chame `Blazor.start` e passe um objeto de configuração que especifica o SignalR Construtor.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="0ffd4-200">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="0ffd4-200">Logging</span></span>

<span data-ttu-id="0ffd4-201">Para obter informações Blazor sobre o suporte a logs <xref:fundamentals/logging/index#create-logs-in-blazor>de servidor, consulte.</span><span class="sxs-lookup"><span data-stu-id="0ffd4-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
