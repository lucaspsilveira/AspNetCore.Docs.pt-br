---
title: Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0
author: pakrym
description: Saiba como migrar um aplicativo non-ASP.NET Core que usa Microsoft. Extensions. Logging de 2,1 para 2,2 ou 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777053"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="78f35-103">Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0</span><span class="sxs-lookup"><span data-stu-id="78f35-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="78f35-104">Este artigo descreve as etapas comuns para migrar um aplicativo non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 para 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="78f35-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="78f35-105">2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="78f35-105">2.1 to 2.2</span></span>

<span data-ttu-id="78f35-106">Criar `ServiceCollection` e chamar `AddLogging`manualmente.</span><span class="sxs-lookup"><span data-stu-id="78f35-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="78f35-107">2,1 exemplo:</span><span class="sxs-lookup"><span data-stu-id="78f35-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="78f35-108">2,2 exemplo:</span><span class="sxs-lookup"><span data-stu-id="78f35-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="78f35-109">2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="78f35-109">2.1 to 3.0</span></span>

<span data-ttu-id="78f35-110">Em 3,0, use `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="78f35-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="78f35-111">2,1 exemplo:</span><span class="sxs-lookup"><span data-stu-id="78f35-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="78f35-112">3,0 exemplo:</span><span class="sxs-lookup"><span data-stu-id="78f35-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="78f35-113">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="78f35-113">Additional resources</span></span>

<xref:fundamentals/logging/index>