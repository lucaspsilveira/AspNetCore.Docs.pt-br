---
title: Log de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre como fazer logon em Blazor aplicativos, incluindo a configuração de nível de log e como gravar mensagens de log de Razor componentes.
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 841c4021d9217312b2601b0e775542c6455cca82
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240879"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="3a54f-103">Log de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3a54f-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="3a54f-104">Webassembly</span><span class="sxs-lookup"><span data-stu-id="3a54f-104"> WebAssembly</span></span>

<span data-ttu-id="3a54f-105">Configure o log em Blazor aplicativos Webassembly com a `WebAssemblyHostBuilder.Logging` propriedade em `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="3a54f-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="3a54f-106">A `Logging` propriedade é do tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , portanto, todos os métodos de extensão disponíveis no <xref:Microsoft.Extensions.Logging.ILoggingBuilder> também estão disponíveis no `Logging` .</span><span class="sxs-lookup"><span data-stu-id="3a54f-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="3a54f-107">A configuração de log pode ser carregada de arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3a54f-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="3a54f-108">Para obter mais informações, consulte <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3a54f-108">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="3a54f-109">Servidor</span><span class="sxs-lookup"><span data-stu-id="3a54f-109"> Server</span></span>

<span data-ttu-id="3a54f-110">Para obter diretrizes gerais de log de ASP.NET Core, consulte <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="3a54f-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor<span data-ttu-id="3a54f-111">Log de cliente do Webassembly SignalR .net</span><span class="sxs-lookup"><span data-stu-id="3a54f-111"> WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="3a54f-112">Insira um <xref:Microsoft.Extensions.Logging.ILoggerProvider> para adicionar um `WebAssemblyConsoleLogger` aos provedores de log passados para <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3a54f-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="3a54f-113">Ao contrário de um tradicional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` é um wrapper em relação às APIs de log específicas do navegador (por exemplo, `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="3a54f-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="3a54f-114">O uso de `WebAssemblyConsoleLogger` torna o registro em log possível no mono dentro de um contexto do navegador.</span><span class="sxs-lookup"><span data-stu-id="3a54f-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="3a54f-115">Componentes de logon Razor</span><span class="sxs-lookup"><span data-stu-id="3a54f-115">Log in Razor components</span></span>

<span data-ttu-id="3a54f-116">Os agentes respeitam a configuração de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3a54f-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="3a54f-117">A `using` diretiva do <xref:Microsoft.Extensions.Logging> é necessária para dar suporte a conclusões do IntelliSense para APIs, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="3a54f-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="3a54f-118">O exemplo a seguir demonstra o registro em log com um <xref:Microsoft.Extensions.Logging.ILogger> nos Razor componentes do:</span><span class="sxs-lookup"><span data-stu-id="3a54f-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="3a54f-119">O exemplo a seguir demonstra o registro em log com um <xref:Microsoft.Extensions.Logging.ILoggerFactory> nos Razor componentes do:</span><span class="sxs-lookup"><span data-stu-id="3a54f-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="3a54f-120">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3a54f-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
