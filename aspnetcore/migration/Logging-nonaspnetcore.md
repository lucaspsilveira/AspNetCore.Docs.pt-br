---
<span data-ttu-id="00ee7-101">Título: migrar de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0 Author: pakrym Descrição: saiba como migrar um aplicativo non-ASP.NET Core que usa Microsoft. Extensions. Logging de 2,1 para 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="00ee7-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="00ee7-102">MS. Author: pakrym MS. Custom: MVC MS. Date: 01/04/2019 no-loc:</span><span class="sxs-lookup"><span data-stu-id="00ee7-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="00ee7-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="00ee7-103">'Blazor'</span></span>
- <span data-ttu-id="00ee7-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="00ee7-104">'Identity'</span></span>
- <span data-ttu-id="00ee7-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="00ee7-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="00ee7-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="00ee7-106">'Razor'</span></span>
- <span data-ttu-id="00ee7-107">SignalRUID: migração/registro em log – nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="00ee7-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="00ee7-108">Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0</span><span class="sxs-lookup"><span data-stu-id="00ee7-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="00ee7-109">Este artigo descreve as etapas comuns para migrar um aplicativo non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 para 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="00ee7-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="00ee7-110">2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="00ee7-110">2.1 to 2.2</span></span>

<span data-ttu-id="00ee7-111">Criar `ServiceCollection` e chamar manualmente `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="00ee7-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="00ee7-112">2,1 exemplo:</span><span class="sxs-lookup"><span data-stu-id="00ee7-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="00ee7-113">2,2 exemplo:</span><span class="sxs-lookup"><span data-stu-id="00ee7-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="00ee7-114">2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="00ee7-114">2.1 to 3.0</span></span>

<span data-ttu-id="00ee7-115">Em 3,0, use `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="00ee7-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="00ee7-116">2,1 exemplo:</span><span class="sxs-lookup"><span data-stu-id="00ee7-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="00ee7-117">3,0 exemplo:</span><span class="sxs-lookup"><span data-stu-id="00ee7-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="00ee7-118">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="00ee7-118">Additional resources</span></span>

* <span data-ttu-id="00ee7-119">[Pacote NuGet Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="00ee7-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
