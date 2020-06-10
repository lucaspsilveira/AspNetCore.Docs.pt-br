---
title: Caching distribuído no ASP.NET Core
author: rick-anderson
description: Saiba como usar um ASP.NET Core cache distribuído para melhorar o desempenho e a escalabilidade do aplicativo, especialmente em um ambiente de nuvem ou de farm de servidores.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: d655e48f9504d337b0ffdbd6819f32101730310b
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106696"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="251d0-103">Caching distribuído no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="251d0-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="251d0-104">Por [Mohsin Nasir](https://github.com/mohsinnasir) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="251d0-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="251d0-105">Um cache distribuído é um cache compartilhado por vários servidores de aplicativos, normalmente mantido como um serviço externo para os servidores de aplicativos que o acessam.</span><span class="sxs-lookup"><span data-stu-id="251d0-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="251d0-106">Um cache distribuído pode melhorar o desempenho e a escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="251d0-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="251d0-107">Um cache distribuído tem várias vantagens em relação A outros cenários de cache em que os dados armazenados em cache são armazenados em servidores de aplicativos individuais.</span><span class="sxs-lookup"><span data-stu-id="251d0-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="251d0-108">Quando os dados armazenados em cache são distribuídos, os dados:</span><span class="sxs-lookup"><span data-stu-id="251d0-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="251d0-109">É *coerente* (consistente) entre solicitações para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="251d0-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="251d0-110">Sobreviver a reinicializações de servidor e implantações de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="251d0-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="251d0-111">Não usa memória local.</span><span class="sxs-lookup"><span data-stu-id="251d0-111">Doesn't use local memory.</span></span>

<span data-ttu-id="251d0-112">A configuração de cache distribuído é específica da implementação.</span><span class="sxs-lookup"><span data-stu-id="251d0-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="251d0-113">Este artigo descreve como configurar os caches distribuídos SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="251d0-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="251d0-114">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="251d0-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="251d0-115">Independentemente da implementação selecionada, o aplicativo interage com o cache usando a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span><span class="sxs-lookup"><span data-stu-id="251d0-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="251d0-116">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="251d0-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="251d0-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="251d0-117">Prerequisites</span></span>

<span data-ttu-id="251d0-118">Para usar um SQL Server cache distribuído, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="251d0-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="251d0-119">Para usar um cache distribuído Redis, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="251d0-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="251d0-120">Para usar o cache distribuído do NCache, adicione uma referência de pacote ao pacote do [NCache. Microsoft. Extensions. Caching. Open](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="251d0-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="251d0-121">Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="251d0-121">IDistributedCache interface</span></span>

<span data-ttu-id="251d0-122">A <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fornece os seguintes métodos para manipular itens na implementação do cache distribuído:</span><span class="sxs-lookup"><span data-stu-id="251d0-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="251d0-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Aceita uma chave de cadeia de caracteres e recupera um item em cache como uma `byte[]` matriz, se encontrado no cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="251d0-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Adiciona um item (como `byte[]` matriz) ao cache usando uma chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="251d0-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="251d0-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).</span><span class="sxs-lookup"><span data-stu-id="251d0-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="251d0-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Remove um item de cache com base em sua chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="251d0-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="251d0-127">Estabelecer serviços de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-127">Establish distributed caching services</span></span>

<span data-ttu-id="251d0-128">Registre uma implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="251d0-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="251d0-129">As implementações fornecidas pelo Framework descritas neste tópico incluem:</span><span class="sxs-lookup"><span data-stu-id="251d0-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="251d0-130">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="251d0-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="251d0-131">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="251d0-132">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="251d0-133">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="251d0-134">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="251d0-134">Distributed Memory Cache</span></span>

<span data-ttu-id="251d0-135">O cache de memória distribuída ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) é uma implementação fornecida pela estrutura do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena itens na memória.</span><span class="sxs-lookup"><span data-stu-id="251d0-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="251d0-136">O cache de memória distribuída não é um cache distribuído real.</span><span class="sxs-lookup"><span data-stu-id="251d0-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="251d0-137">Os itens armazenados em cache são armazenados pela instância do aplicativo no servidor em que o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="251d0-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="251d0-138">O cache de memória distribuída é uma implementação útil:</span><span class="sxs-lookup"><span data-stu-id="251d0-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="251d0-139">Em cenários de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="251d0-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="251d0-140">Quando um único servidor é usado em produção e o consumo de memória não é um problema.</span><span class="sxs-lookup"><span data-stu-id="251d0-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="251d0-141">A implementação do cache de memória distribuída abstrai o armazenamento de dados em cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="251d0-142">Ele permite implementar uma solução de cache distribuído verdadeira no futuro se vários nós ou tolerância a falhas se tornarem necessários.</span><span class="sxs-lookup"><span data-stu-id="251d0-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="251d0-143">O aplicativo de exemplo usa o cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="251d0-144">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="251d0-145">A implementação do cache SQL Server distribuído ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permite que o cache distribuído use um banco de dados SQL Server como seu armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="251d0-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="251d0-146">Para criar uma SQL Server tabela de item em cache em uma instância de SQL Server, você pode usar a `sql-cache` ferramenta.</span><span class="sxs-lookup"><span data-stu-id="251d0-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="251d0-147">A ferramenta cria uma tabela com o nome e o esquema que você especificar.</span><span class="sxs-lookup"><span data-stu-id="251d0-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="251d0-148">Crie uma tabela no SQL Server executando o `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="251d0-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="251d0-149">Forneça a SQL Server instância ( `Data Source` ), banco de dados ( `Initial Catalog` ), esquema (por exemplo, `dbo` ) e nome da tabela (por exemplo, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="251d0-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="251d0-150">Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="251d0-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="251d0-151">A tabela criada pela `sql-cache` ferramenta tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="251d0-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela de cache do SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="251d0-153">Um aplicativo deve manipular valores de cache usando uma instância do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="251d0-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="251d0-154">O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de não desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="251d0-155">Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) normalmente são armazenados fora do controle do código-fonte (por exemplo, armazenado pelo [Gerenciador de segredo](xref:security/app-secrets) ou em *appSettings. JSON* / *appSettings. { Arquivos de ambiente}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="251d0-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="251d0-156">A cadeia de conexão pode conter credenciais que devem ser mantidas fora dos sistemas de controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="251d0-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="251d0-157">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-157">Distributed Redis Cache</span></span>

<span data-ttu-id="251d0-158">O [Redis](https://redis.io/) é um armazenamento de dados na memória de software livre, que geralmente é usado como um cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="251d0-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="251d0-159">Você pode usar o Redis localmente e pode configurar um [cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo de ASP.NET Core hospedado pelo Azure.</span><span class="sxs-lookup"><span data-stu-id="251d0-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="251d0-160">Um aplicativo configura a implementação do cache usando uma <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instância ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) em um ambiente de não desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="251d0-161">Para instalar o Redis em seu computador local:</span><span class="sxs-lookup"><span data-stu-id="251d0-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="251d0-162">Instale o [pacote Redis de Chocolatey](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="251d0-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="251d0-163">Execute `redis-server` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="251d0-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="251d0-164">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-164">Distributed NCache Cache</span></span>

<span data-ttu-id="251d0-165">O [NCache](https://github.com/Alachisoft/NCache) é um cache distribuído na memória de código aberto desenvolvido nativamente no .net e no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="251d0-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="251d0-166">O NCache funciona tanto localmente quanto configurado como um cluster de cache distribuído para um aplicativo ASP.NET Core em execução no Azure ou em outras plataformas de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="251d0-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="251d0-167">Para instalar e configurar o NCache em seu computador local, consulte [Guia do ncache introdução para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="251d0-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="251d0-168">Para configurar o NCache:</span><span class="sxs-lookup"><span data-stu-id="251d0-168">To configure NCache:</span></span>

1. <span data-ttu-id="251d0-169">Instale o [NuGet de código-fonte aberto do NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="251d0-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="251d0-170">Configure o cluster de cache em [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="251d0-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="251d0-171">Adicione o seguinte código a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="251d0-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="251d0-172">Usar o cache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-172">Use the distributed cache</span></span>

<span data-ttu-id="251d0-173">Para usar a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, solicite uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer Construtor no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="251d0-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="251d0-174">A instância é fornecida pela [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="251d0-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="251d0-175">Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="251d0-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="251d0-176">A hora atual é armazenada em cache usando <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (para obter mais informações, consulte [host genérico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="251d0-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="251d0-177">O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `IndexModel` para ser usado pela página de índice.</span><span class="sxs-lookup"><span data-stu-id="251d0-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="251d0-178">Cada vez que a página de índice é carregada, o cache é verificado quanto ao tempo em cache `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="251d0-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="251d0-179">Se o tempo em cache não tiver expirado, a hora será exibida.</span><span class="sxs-lookup"><span data-stu-id="251d0-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="251d0-180">Se 20 segundos tiverem decorrido desde a última vez em que o tempo de cache foi acessado (na última vez em que essa página foi carregada), a página exibirá o *tempo de cache expirado*.</span><span class="sxs-lookup"><span data-stu-id="251d0-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="251d0-181">Atualize imediatamente a hora em cache para a hora atual selecionando o botão **Redefinir tempo de cache** .</span><span class="sxs-lookup"><span data-stu-id="251d0-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="251d0-182">O botão dispara o `OnPostResetCachedTime` método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="251d0-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="251d0-183">Não é necessário usar um singleton ou tempo de vida com escopo para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instâncias (pelo menos para as implementações internas).</span><span class="sxs-lookup"><span data-stu-id="251d0-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="251d0-184">Você também pode criar uma <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instância onde você pode precisar de uma em vez de usar di, mas criar uma instância no código pode tornar seu código mais difícil de testar e violar o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="251d0-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="251d0-185">Recomendações</span><span class="sxs-lookup"><span data-stu-id="251d0-185">Recommendations</span></span>

<span data-ttu-id="251d0-186">Ao decidir qual implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:</span><span class="sxs-lookup"><span data-stu-id="251d0-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="251d0-187">Infraestrutura existente</span><span class="sxs-lookup"><span data-stu-id="251d0-187">Existing infrastructure</span></span>
* <span data-ttu-id="251d0-188">Requisitos de desempenho</span><span class="sxs-lookup"><span data-stu-id="251d0-188">Performance requirements</span></span>
* <span data-ttu-id="251d0-189">Custo</span><span class="sxs-lookup"><span data-stu-id="251d0-189">Cost</span></span>
* <span data-ttu-id="251d0-190">Experiência da equipe</span><span class="sxs-lookup"><span data-stu-id="251d0-190">Team experience</span></span>

<span data-ttu-id="251d0-191">As soluções de cache geralmente dependem do armazenamento na memória para fornecer uma recuperação rápida dos dados armazenados em cache, mas a memória é um recurso limitado e dispendiosa de expandir.</span><span class="sxs-lookup"><span data-stu-id="251d0-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="251d0-192">Armazene somente dados usados em um cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="251d0-193">Geralmente, um cache Redis fornece maior taxa de transferência e menor latência do que um cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="251d0-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="251d0-194">No entanto, o benchmark geralmente é necessário para determinar as características de desempenho das estratégias de cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="251d0-195">Quando SQL Server é usado como um armazenamento de backup de cache distribuído, o uso do mesmo banco de dados para o cache e o armazenamento e a recuperação comuns do aplicativo podem afetar negativamente o desempenho de ambos.</span><span class="sxs-lookup"><span data-stu-id="251d0-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="251d0-196">É recomendável usar uma instância de SQL Server dedicada para o armazenamento de backup de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="251d0-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="251d0-197">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="251d0-197">Additional resources</span></span>

* [<span data-ttu-id="251d0-198">Cache Redis no Azure</span><span class="sxs-lookup"><span data-stu-id="251d0-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="251d0-199">Banco de dados SQL no Azure</span><span class="sxs-lookup"><span data-stu-id="251d0-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="251d0-200">[ASP.NET Core o provedor IDistributedCache para o NCache em farms da Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="251d0-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="251d0-201">Um cache distribuído é um cache compartilhado por vários servidores de aplicativos, normalmente mantido como um serviço externo para os servidores de aplicativos que o acessam.</span><span class="sxs-lookup"><span data-stu-id="251d0-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="251d0-202">Um cache distribuído pode melhorar o desempenho e a escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="251d0-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="251d0-203">Um cache distribuído tem várias vantagens em relação A outros cenários de cache em que os dados armazenados em cache são armazenados em servidores de aplicativos individuais.</span><span class="sxs-lookup"><span data-stu-id="251d0-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="251d0-204">Quando os dados armazenados em cache são distribuídos, os dados:</span><span class="sxs-lookup"><span data-stu-id="251d0-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="251d0-205">É *coerente* (consistente) entre solicitações para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="251d0-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="251d0-206">Sobreviver a reinicializações de servidor e implantações de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="251d0-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="251d0-207">Não usa memória local.</span><span class="sxs-lookup"><span data-stu-id="251d0-207">Doesn't use local memory.</span></span>

<span data-ttu-id="251d0-208">A configuração de cache distribuído é específica da implementação.</span><span class="sxs-lookup"><span data-stu-id="251d0-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="251d0-209">Este artigo descreve como configurar os caches distribuídos SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="251d0-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="251d0-210">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="251d0-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="251d0-211">Independentemente da implementação selecionada, o aplicativo interage com o cache usando a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span><span class="sxs-lookup"><span data-stu-id="251d0-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="251d0-212">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="251d0-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="251d0-213">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="251d0-213">Prerequisites</span></span>

<span data-ttu-id="251d0-214">Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="251d0-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="251d0-215">Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="251d0-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="251d0-216">O pacote Redis não está incluído no `Microsoft.AspNetCore.App` pacote, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="251d0-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="251d0-217">Para usar o cache distribuído do NCache, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [NCache. Microsoft. Extensions. Caching. openize](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="251d0-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="251d0-218">O pacote do NCache não está incluído no `Microsoft.AspNetCore.App` pacote, portanto, você deve fazer referência ao pacote do NCache separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="251d0-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="251d0-219">Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="251d0-219">IDistributedCache interface</span></span>

<span data-ttu-id="251d0-220">A <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fornece os seguintes métodos para manipular itens na implementação do cache distribuído:</span><span class="sxs-lookup"><span data-stu-id="251d0-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="251d0-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Aceita uma chave de cadeia de caracteres e recupera um item em cache como uma `byte[]` matriz, se encontrado no cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="251d0-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Adiciona um item (como `byte[]` matriz) ao cache usando uma chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="251d0-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="251d0-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).</span><span class="sxs-lookup"><span data-stu-id="251d0-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="251d0-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Remove um item de cache com base em sua chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="251d0-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="251d0-225">Estabelecer serviços de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-225">Establish distributed caching services</span></span>

<span data-ttu-id="251d0-226">Registre uma implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="251d0-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="251d0-227">As implementações fornecidas pelo Framework descritas neste tópico incluem:</span><span class="sxs-lookup"><span data-stu-id="251d0-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="251d0-228">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="251d0-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="251d0-229">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="251d0-230">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="251d0-231">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="251d0-232">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="251d0-232">Distributed Memory Cache</span></span>

<span data-ttu-id="251d0-233">O cache de memória distribuída ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) é uma implementação fornecida pela estrutura do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena itens na memória.</span><span class="sxs-lookup"><span data-stu-id="251d0-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="251d0-234">O cache de memória distribuída não é um cache distribuído real.</span><span class="sxs-lookup"><span data-stu-id="251d0-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="251d0-235">Os itens armazenados em cache são armazenados pela instância do aplicativo no servidor em que o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="251d0-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="251d0-236">O cache de memória distribuída é uma implementação útil:</span><span class="sxs-lookup"><span data-stu-id="251d0-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="251d0-237">Em cenários de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="251d0-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="251d0-238">Quando um único servidor é usado em produção e o consumo de memória não é um problema.</span><span class="sxs-lookup"><span data-stu-id="251d0-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="251d0-239">A implementação do cache de memória distribuída abstrai o armazenamento de dados em cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="251d0-240">Ele permite implementar uma solução de cache distribuído verdadeira no futuro se vários nós ou tolerância a falhas se tornarem necessários.</span><span class="sxs-lookup"><span data-stu-id="251d0-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="251d0-241">O aplicativo de exemplo usa o cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="251d0-242">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="251d0-243">A implementação do cache SQL Server distribuído ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permite que o cache distribuído use um banco de dados SQL Server como seu armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="251d0-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="251d0-244">Para criar uma SQL Server tabela de item em cache em uma instância de SQL Server, você pode usar a `sql-cache` ferramenta.</span><span class="sxs-lookup"><span data-stu-id="251d0-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="251d0-245">A ferramenta cria uma tabela com o nome e o esquema que você especificar.</span><span class="sxs-lookup"><span data-stu-id="251d0-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="251d0-246">Crie uma tabela no SQL Server executando o `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="251d0-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="251d0-247">Forneça a SQL Server instância ( `Data Source` ), banco de dados ( `Initial Catalog` ), esquema (por exemplo, `dbo` ) e nome da tabela (por exemplo, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="251d0-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="251d0-248">Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="251d0-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="251d0-249">A tabela criada pela `sql-cache` ferramenta tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="251d0-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela de cache do SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="251d0-251">Um aplicativo deve manipular valores de cache usando uma instância do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="251d0-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="251d0-252">O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de não desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="251d0-253">Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) normalmente são armazenados fora do controle do código-fonte (por exemplo, armazenado pelo [Gerenciador de segredo](xref:security/app-secrets) ou em *appSettings. JSON* / *appSettings. { Arquivos de ambiente}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="251d0-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="251d0-254">A cadeia de conexão pode conter credenciais que devem ser mantidas fora dos sistemas de controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="251d0-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="251d0-255">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-255">Distributed Redis Cache</span></span>

<span data-ttu-id="251d0-256">O [Redis](https://redis.io/) é um armazenamento de dados na memória de software livre, que geralmente é usado como um cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="251d0-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="251d0-257">Você pode usar o Redis localmente e pode configurar um [cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo de ASP.NET Core hospedado pelo Azure.</span><span class="sxs-lookup"><span data-stu-id="251d0-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="251d0-258">Um aplicativo configura a implementação do cache usando uma <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instância ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) em um ambiente de não desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="251d0-259">Para instalar o Redis em seu computador local:</span><span class="sxs-lookup"><span data-stu-id="251d0-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="251d0-260">Instale o [pacote Redis de Chocolatey](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="251d0-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="251d0-261">Execute `redis-server` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="251d0-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="251d0-262">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-262">Distributed NCache Cache</span></span>

<span data-ttu-id="251d0-263">O [NCache](https://github.com/Alachisoft/NCache) é um cache distribuído na memória de código aberto desenvolvido nativamente no .net e no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="251d0-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="251d0-264">O NCache funciona tanto localmente quanto configurado como um cluster de cache distribuído para um aplicativo ASP.NET Core em execução no Azure ou em outras plataformas de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="251d0-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="251d0-265">Para instalar e configurar o NCache em seu computador local, consulte [Guia do ncache introdução para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="251d0-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="251d0-266">Para configurar o NCache:</span><span class="sxs-lookup"><span data-stu-id="251d0-266">To configure NCache:</span></span>

1. <span data-ttu-id="251d0-267">Instale o [NuGet de código-fonte aberto do NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="251d0-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="251d0-268">Configure o cluster de cache em [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="251d0-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="251d0-269">Adicione o seguinte código a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="251d0-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="251d0-270">Usar o cache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-270">Use the distributed cache</span></span>

<span data-ttu-id="251d0-271">Para usar a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, solicite uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer Construtor no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="251d0-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="251d0-272">A instância é fornecida pela [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="251d0-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="251d0-273">Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="251d0-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="251d0-274">A hora atual é armazenada em cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obter mais informações, consulte [Web host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="251d0-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="251d0-275">O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `IndexModel` para ser usado pela página de índice.</span><span class="sxs-lookup"><span data-stu-id="251d0-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="251d0-276">Cada vez que a página de índice é carregada, o cache é verificado quanto ao tempo em cache `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="251d0-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="251d0-277">Se o tempo em cache não tiver expirado, a hora será exibida.</span><span class="sxs-lookup"><span data-stu-id="251d0-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="251d0-278">Se 20 segundos tiverem decorrido desde a última vez em que o tempo de cache foi acessado (na última vez em que essa página foi carregada), a página exibirá o *tempo de cache expirado*.</span><span class="sxs-lookup"><span data-stu-id="251d0-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="251d0-279">Atualize imediatamente a hora em cache para a hora atual selecionando o botão **Redefinir tempo de cache** .</span><span class="sxs-lookup"><span data-stu-id="251d0-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="251d0-280">O botão dispara o `OnPostResetCachedTime` método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="251d0-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="251d0-281">Não é necessário usar um singleton ou tempo de vida com escopo para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instâncias (pelo menos para as implementações internas).</span><span class="sxs-lookup"><span data-stu-id="251d0-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="251d0-282">Você também pode criar uma <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instância onde você pode precisar de uma em vez de usar di, mas criar uma instância no código pode tornar seu código mais difícil de testar e violar o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="251d0-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="251d0-283">Recomendações</span><span class="sxs-lookup"><span data-stu-id="251d0-283">Recommendations</span></span>

<span data-ttu-id="251d0-284">Ao decidir qual implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:</span><span class="sxs-lookup"><span data-stu-id="251d0-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="251d0-285">Infraestrutura existente</span><span class="sxs-lookup"><span data-stu-id="251d0-285">Existing infrastructure</span></span>
* <span data-ttu-id="251d0-286">Requisitos de desempenho</span><span class="sxs-lookup"><span data-stu-id="251d0-286">Performance requirements</span></span>
* <span data-ttu-id="251d0-287">Custo</span><span class="sxs-lookup"><span data-stu-id="251d0-287">Cost</span></span>
* <span data-ttu-id="251d0-288">Experiência da equipe</span><span class="sxs-lookup"><span data-stu-id="251d0-288">Team experience</span></span>

<span data-ttu-id="251d0-289">As soluções de cache geralmente dependem do armazenamento na memória para fornecer uma recuperação rápida dos dados armazenados em cache, mas a memória é um recurso limitado e dispendiosa de expandir.</span><span class="sxs-lookup"><span data-stu-id="251d0-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="251d0-290">Armazene somente dados usados em um cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="251d0-291">Geralmente, um cache Redis fornece maior taxa de transferência e menor latência do que um cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="251d0-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="251d0-292">No entanto, o benchmark geralmente é necessário para determinar as características de desempenho das estratégias de cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="251d0-293">Quando SQL Server é usado como um armazenamento de backup de cache distribuído, o uso do mesmo banco de dados para o cache e o armazenamento e a recuperação comuns do aplicativo podem afetar negativamente o desempenho de ambos.</span><span class="sxs-lookup"><span data-stu-id="251d0-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="251d0-294">É recomendável usar uma instância de SQL Server dedicada para o armazenamento de backup de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="251d0-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="251d0-295">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="251d0-295">Additional resources</span></span>

* [<span data-ttu-id="251d0-296">Cache Redis no Azure</span><span class="sxs-lookup"><span data-stu-id="251d0-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="251d0-297">Banco de dados SQL no Azure</span><span class="sxs-lookup"><span data-stu-id="251d0-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="251d0-298">[ASP.NET Core o provedor IDistributedCache para o NCache em farms da Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="251d0-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="251d0-299">Um cache distribuído é um cache compartilhado por vários servidores de aplicativos, normalmente mantido como um serviço externo para os servidores de aplicativos que o acessam.</span><span class="sxs-lookup"><span data-stu-id="251d0-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="251d0-300">Um cache distribuído pode melhorar o desempenho e a escalabilidade de um aplicativo ASP.NET Core, especialmente quando o aplicativo é hospedado por um serviço de nuvem ou um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="251d0-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="251d0-301">Um cache distribuído tem várias vantagens em relação A outros cenários de cache em que os dados armazenados em cache são armazenados em servidores de aplicativos individuais.</span><span class="sxs-lookup"><span data-stu-id="251d0-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="251d0-302">Quando os dados armazenados em cache são distribuídos, os dados:</span><span class="sxs-lookup"><span data-stu-id="251d0-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="251d0-303">É *coerente* (consistente) entre solicitações para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="251d0-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="251d0-304">Sobreviver a reinicializações de servidor e implantações de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="251d0-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="251d0-305">Não usa memória local.</span><span class="sxs-lookup"><span data-stu-id="251d0-305">Doesn't use local memory.</span></span>

<span data-ttu-id="251d0-306">A configuração de cache distribuído é específica da implementação.</span><span class="sxs-lookup"><span data-stu-id="251d0-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="251d0-307">Este artigo descreve como configurar os caches distribuídos SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="251d0-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="251d0-308">Implementações de terceiros também estão disponíveis, como o [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="251d0-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="251d0-309">Independentemente da implementação selecionada, o aplicativo interage com o cache usando a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span><span class="sxs-lookup"><span data-stu-id="251d0-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="251d0-310">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="251d0-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="251d0-311">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="251d0-311">Prerequisites</span></span>

<span data-ttu-id="251d0-312">Para usar um SQL Server cache distribuído, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="251d0-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="251d0-313">Para usar um cache distribuído Redis, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="251d0-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="251d0-314">O pacote Redis não está incluído no `Microsoft.AspNetCore.App` pacote, portanto, você deve fazer referência ao pacote Redis separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="251d0-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="251d0-315">Para usar o cache distribuído do NCache, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e adicione uma referência de pacote ao pacote [NCache. Microsoft. Extensions. Caching. openize](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="251d0-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="251d0-316">O pacote do NCache não está incluído no `Microsoft.AspNetCore.App` pacote, portanto, você deve fazer referência ao pacote do NCache separadamente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="251d0-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="251d0-317">Interface IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="251d0-317">IDistributedCache interface</span></span>

<span data-ttu-id="251d0-318">A <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface fornece os seguintes métodos para manipular itens na implementação do cache distribuído:</span><span class="sxs-lookup"><span data-stu-id="251d0-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="251d0-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Aceita uma chave de cadeia de caracteres e recupera um item em cache como uma `byte[]` matriz, se encontrado no cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="251d0-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Adiciona um item (como `byte[]` matriz) ao cache usando uma chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="251d0-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="251d0-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Atualiza um item no cache com base em sua chave, redefinindo seu tempo limite de expiração deslizante (se houver).</span><span class="sxs-lookup"><span data-stu-id="251d0-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="251d0-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Remove um item de cache com base em sua chave de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="251d0-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="251d0-323">Estabelecer serviços de cache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-323">Establish distributed caching services</span></span>

<span data-ttu-id="251d0-324">Registre uma implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="251d0-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="251d0-325">As implementações fornecidas pelo Framework descritas neste tópico incluem:</span><span class="sxs-lookup"><span data-stu-id="251d0-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="251d0-326">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="251d0-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="251d0-327">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="251d0-328">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="251d0-329">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="251d0-330">Cache de memória distribuída</span><span class="sxs-lookup"><span data-stu-id="251d0-330">Distributed Memory Cache</span></span>

<span data-ttu-id="251d0-331">O cache de memória distribuída ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) é uma implementação fornecida pela estrutura do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> que armazena itens na memória.</span><span class="sxs-lookup"><span data-stu-id="251d0-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="251d0-332">O cache de memória distribuída não é um cache distribuído real.</span><span class="sxs-lookup"><span data-stu-id="251d0-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="251d0-333">Os itens armazenados em cache são armazenados pela instância do aplicativo no servidor em que o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="251d0-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="251d0-334">O cache de memória distribuída é uma implementação útil:</span><span class="sxs-lookup"><span data-stu-id="251d0-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="251d0-335">Em cenários de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="251d0-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="251d0-336">Quando um único servidor é usado em produção e o consumo de memória não é um problema.</span><span class="sxs-lookup"><span data-stu-id="251d0-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="251d0-337">A implementação do cache de memória distribuída abstrai o armazenamento de dados em cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="251d0-338">Ele permite implementar uma solução de cache distribuído verdadeira no futuro se vários nós ou tolerância a falhas se tornarem necessários.</span><span class="sxs-lookup"><span data-stu-id="251d0-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="251d0-339">O aplicativo de exemplo usa o cache de memória distribuída quando o aplicativo é executado no ambiente de desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="251d0-340">Cache SQL Server distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="251d0-341">A implementação do cache SQL Server distribuído ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) permite que o cache distribuído use um banco de dados SQL Server como seu armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="251d0-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="251d0-342">Para criar uma SQL Server tabela de item em cache em uma instância de SQL Server, você pode usar a `sql-cache` ferramenta.</span><span class="sxs-lookup"><span data-stu-id="251d0-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="251d0-343">A ferramenta cria uma tabela com o nome e o esquema que você especificar.</span><span class="sxs-lookup"><span data-stu-id="251d0-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="251d0-344">Crie uma tabela no SQL Server executando o `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="251d0-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="251d0-345">Forneça a SQL Server instância ( `Data Source` ), banco de dados ( `Initial Catalog` ), esquema (por exemplo, `dbo` ) e nome da tabela (por exemplo, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="251d0-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="251d0-346">Uma mensagem é registrada para indicar que a ferramenta foi bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="251d0-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="251d0-347">A tabela criada pela `sql-cache` ferramenta tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="251d0-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabela de cache do SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="251d0-349">Um aplicativo deve manipular valores de cache usando uma instância do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , não um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="251d0-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="251d0-350">O aplicativo de exemplo implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> em um ambiente de não desenvolvimento no `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="251d0-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="251d0-351">Um <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, opcionalmente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) normalmente são armazenados fora do controle do código-fonte (por exemplo, armazenado pelo [Gerenciador de segredo](xref:security/app-secrets) ou em *appSettings. JSON* / *appSettings. { Arquivos de ambiente}. JSON* ).</span><span class="sxs-lookup"><span data-stu-id="251d0-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="251d0-352">A cadeia de conexão pode conter credenciais que devem ser mantidas fora dos sistemas de controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="251d0-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="251d0-353">Cache Redis distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-353">Distributed Redis Cache</span></span>

<span data-ttu-id="251d0-354">O [Redis](https://redis.io/) é um armazenamento de dados na memória de software livre, que geralmente é usado como um cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="251d0-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="251d0-355">Você pode usar o Redis localmente e pode configurar um [cache Redis do Azure](https://azure.microsoft.com/services/cache/) para um aplicativo de ASP.NET Core hospedado pelo Azure.</span><span class="sxs-lookup"><span data-stu-id="251d0-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="251d0-356">Um aplicativo configura a implementação do cache usando uma <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instância ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):</span><span class="sxs-lookup"><span data-stu-id="251d0-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="251d0-357">Para instalar o Redis em seu computador local:</span><span class="sxs-lookup"><span data-stu-id="251d0-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="251d0-358">Instale o [pacote Redis de Chocolatey](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="251d0-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="251d0-359">Execute `redis-server` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="251d0-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="251d0-360">Cache do NCache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-360">Distributed NCache Cache</span></span>

<span data-ttu-id="251d0-361">O [NCache](https://github.com/Alachisoft/NCache) é um cache distribuído na memória de código aberto desenvolvido nativamente no .net e no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="251d0-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="251d0-362">O NCache funciona tanto localmente quanto configurado como um cluster de cache distribuído para um aplicativo ASP.NET Core em execução no Azure ou em outras plataformas de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="251d0-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="251d0-363">Para instalar e configurar o NCache em seu computador local, consulte [Guia do ncache introdução para Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="251d0-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="251d0-364">Para configurar o NCache:</span><span class="sxs-lookup"><span data-stu-id="251d0-364">To configure NCache:</span></span>

1. <span data-ttu-id="251d0-365">Instale o [NuGet de código-fonte aberto do NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="251d0-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="251d0-366">Configure o cluster de cache em [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="251d0-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="251d0-367">Adicione o seguinte código a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="251d0-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="251d0-368">Usar o cache distribuído</span><span class="sxs-lookup"><span data-stu-id="251d0-368">Use the distributed cache</span></span>

<span data-ttu-id="251d0-369">Para usar a <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, solicite uma instância de <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> de qualquer Construtor no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="251d0-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="251d0-370">A instância é fornecida pela [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="251d0-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="251d0-371">Quando o aplicativo de exemplo é iniciado, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é injetado no `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="251d0-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="251d0-372">A hora atual é armazenada em cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (para obter mais informações, consulte [Web host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="251d0-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="251d0-373">O aplicativo de exemplo injeta <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> no `IndexModel` para ser usado pela página de índice.</span><span class="sxs-lookup"><span data-stu-id="251d0-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="251d0-374">Cada vez que a página de índice é carregada, o cache é verificado quanto ao tempo em cache `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="251d0-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="251d0-375">Se o tempo em cache não tiver expirado, a hora será exibida.</span><span class="sxs-lookup"><span data-stu-id="251d0-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="251d0-376">Se 20 segundos tiverem decorrido desde a última vez em que o tempo de cache foi acessado (na última vez em que essa página foi carregada), a página exibirá o *tempo de cache expirado*.</span><span class="sxs-lookup"><span data-stu-id="251d0-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="251d0-377">Atualize imediatamente a hora em cache para a hora atual selecionando o botão **Redefinir tempo de cache** .</span><span class="sxs-lookup"><span data-stu-id="251d0-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="251d0-378">O botão dispara o `OnPostResetCachedTime` método do manipulador.</span><span class="sxs-lookup"><span data-stu-id="251d0-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="251d0-379">Não é necessário usar um singleton ou tempo de vida com escopo para <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instâncias (pelo menos para as implementações internas).</span><span class="sxs-lookup"><span data-stu-id="251d0-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="251d0-380">Você também pode criar uma <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instância onde você pode precisar de uma em vez de usar di, mas criar uma instância no código pode tornar seu código mais difícil de testar e violar o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="251d0-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="251d0-381">Recomendações</span><span class="sxs-lookup"><span data-stu-id="251d0-381">Recommendations</span></span>

<span data-ttu-id="251d0-382">Ao decidir qual implementação do <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> é melhor para seu aplicativo, considere o seguinte:</span><span class="sxs-lookup"><span data-stu-id="251d0-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="251d0-383">Infraestrutura existente</span><span class="sxs-lookup"><span data-stu-id="251d0-383">Existing infrastructure</span></span>
* <span data-ttu-id="251d0-384">Requisitos de desempenho</span><span class="sxs-lookup"><span data-stu-id="251d0-384">Performance requirements</span></span>
* <span data-ttu-id="251d0-385">Custo</span><span class="sxs-lookup"><span data-stu-id="251d0-385">Cost</span></span>
* <span data-ttu-id="251d0-386">Experiência da equipe</span><span class="sxs-lookup"><span data-stu-id="251d0-386">Team experience</span></span>

<span data-ttu-id="251d0-387">As soluções de cache geralmente dependem do armazenamento na memória para fornecer uma recuperação rápida dos dados armazenados em cache, mas a memória é um recurso limitado e dispendiosa de expandir.</span><span class="sxs-lookup"><span data-stu-id="251d0-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="251d0-388">Armazene somente dados usados em um cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="251d0-389">Geralmente, um cache Redis fornece maior taxa de transferência e menor latência do que um cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="251d0-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="251d0-390">No entanto, o benchmark geralmente é necessário para determinar as características de desempenho das estratégias de cache.</span><span class="sxs-lookup"><span data-stu-id="251d0-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="251d0-391">Quando SQL Server é usado como um armazenamento de backup de cache distribuído, o uso do mesmo banco de dados para o cache e o armazenamento e a recuperação comuns do aplicativo podem afetar negativamente o desempenho de ambos.</span><span class="sxs-lookup"><span data-stu-id="251d0-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="251d0-392">É recomendável usar uma instância de SQL Server dedicada para o armazenamento de backup de cache distribuído.</span><span class="sxs-lookup"><span data-stu-id="251d0-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="251d0-393">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="251d0-393">Additional resources</span></span>

* [<span data-ttu-id="251d0-394">Cache Redis no Azure</span><span class="sxs-lookup"><span data-stu-id="251d0-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="251d0-395">Banco de dados SQL no Azure</span><span class="sxs-lookup"><span data-stu-id="251d0-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="251d0-396">[ASP.NET Core o provedor IDistributedCache para o NCache em farms da Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache no GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="251d0-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 