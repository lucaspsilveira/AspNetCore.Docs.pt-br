---
title: Configuração de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre a configuração de Blazor aplicativos, incluindo configurações de aplicativo, autenticação e configuração de log.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 0e36b81d771b07e85158724c02210ee50a3ab118
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242674"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="4f08b-103">Configuração de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4f08b-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="4f08b-104">Este tópico aplica-se ao Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="4f08b-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="4f08b-105">Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="4f08b-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="4f08b-106">O Webassembly carrega a configuração de:</span><span class="sxs-lookup"><span data-stu-id="4f08b-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="4f08b-107">Arquivos de configurações do aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="4f08b-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="4f08b-108">Outros [provedores de configuração](xref:fundamentals/configuration/index) registrados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f08b-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="4f08b-109">Nem todos os provedores são apropriados para Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="4f08b-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="4f08b-110">O esclarecimento sobre quais provedores têm suporte para o Blazor Webassembly é acompanhado pelos [provedores de configuração do Clarify para Blazor WASM (dotNet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="4f08b-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="4f08b-111">A configuração em um Blazor aplicativo Webassembly é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="4f08b-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="4f08b-112">**Não armazene os segredos ou as credenciais do aplicativo na configuração.**</span><span class="sxs-lookup"><span data-stu-id="4f08b-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="4f08b-113">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="4f08b-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="4f08b-114">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4f08b-114">App settings configuration</span></span>

<span data-ttu-id="4f08b-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="4f08b-116">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="4f08b-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="4f08b-117">Configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="4f08b-117">Provider configuration</span></span>

<span data-ttu-id="4f08b-118">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para fornecer configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="4f08b-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="4f08b-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-119">`Program.Main`:</span></span>

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

<span data-ttu-id="4f08b-120">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="4f08b-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="4f08b-121">Para ler outros arquivos de configuração da `wwwroot` pasta para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4f08b-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="4f08b-122">Ao usar essa abordagem, o <xref:System.Net.Http.HttpClient> registro de serviço existente pode usar o cliente local criado para ler o arquivo, como mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4f08b-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="4f08b-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="4f08b-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-124">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="4f08b-125">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="4f08b-125">Authentication configuration</span></span>

<span data-ttu-id="4f08b-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="4f08b-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="4f08b-128">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="4f08b-128">Logging configuration</span></span>

<span data-ttu-id="4f08b-129">Adicione uma referência de pacote para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="4f08b-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="4f08b-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="4f08b-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="4f08b-132">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="4f08b-132">Host builder configuration</span></span>

<span data-ttu-id="4f08b-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f08b-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="4f08b-134">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="4f08b-134">Cached configuration</span></span>

<span data-ttu-id="4f08b-135">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="4f08b-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="4f08b-136">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="4f08b-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="4f08b-137">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="4f08b-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="4f08b-138">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="4f08b-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="4f08b-139">Os `service-worker.js` arquivos e do PWA `service-worker-assets.js` devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="4f08b-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="4f08b-140">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="4f08b-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
