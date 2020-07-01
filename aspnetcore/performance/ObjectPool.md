---
title: Reutilização de objeto com objectpool no ASP.NET Core
author: rick-anderson
description: Dicas para aumentar o desempenho em aplicativos ASP.NET Core usando o objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 9df7f370eb550172493478bcd8d94a9541926fec
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793554"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="ea070-103">Reutilização de objeto com objectpool no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea070-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="ea070-104">Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)e [Günther Foidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="ea070-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="ea070-105"><xref:Microsoft.Extensions.ObjectPool>faz parte da infraestrutura de ASP.NET Core que dá suporte à manutenção de um grupo de objetos na memória para reutilização em vez de permitir que os objetos sejam coletados como lixo.</span><span class="sxs-lookup"><span data-stu-id="ea070-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="ea070-106">Talvez você queira usar o pool de objetos se os objetos que estão sendo gerenciados forem:</span><span class="sxs-lookup"><span data-stu-id="ea070-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="ea070-107">Caro de alocar/inicializar.</span><span class="sxs-lookup"><span data-stu-id="ea070-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="ea070-108">Representam alguns recursos limitados.</span><span class="sxs-lookup"><span data-stu-id="ea070-108">Represent some limited resource.</span></span>
- <span data-ttu-id="ea070-109">Usado de forma previsível e frequente.</span><span class="sxs-lookup"><span data-stu-id="ea070-109">Used predictably and frequently.</span></span>

<span data-ttu-id="ea070-110">Por exemplo, o ASP.NET Core Framework usa o pool de objetos em alguns lugares para reutilizar <xref:System.Text.StringBuilder> instâncias.</span><span class="sxs-lookup"><span data-stu-id="ea070-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="ea070-111">`StringBuilder`Aloca e gerencia seus próprios buffers para armazenar dados de caractere.</span><span class="sxs-lookup"><span data-stu-id="ea070-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="ea070-112">O ASP.NET Core usa regularmente `StringBuilder` para implementar recursos e reusá-los proporciona um benefício de desempenho.</span><span class="sxs-lookup"><span data-stu-id="ea070-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="ea070-113">O pool de objetos nem sempre melhora o desempenho:</span><span class="sxs-lookup"><span data-stu-id="ea070-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="ea070-114">A menos que o custo de inicialização de um objeto seja alto, é geralmente mais lento obter o objeto do pool.</span><span class="sxs-lookup"><span data-stu-id="ea070-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="ea070-115">Os objetos gerenciados pelo pool não são desalocados até que o pool seja desalocado.</span><span class="sxs-lookup"><span data-stu-id="ea070-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="ea070-116">Use o pool de objetos somente depois de coletar dados de desempenho usando cenários realistas para seu aplicativo ou biblioteca.</span><span class="sxs-lookup"><span data-stu-id="ea070-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="ea070-117">**Aviso: o `ObjectPool` não implementa `IDisposable` . Não é recomendável usá-lo com tipos que precisam de descarte.**</span><span class="sxs-lookup"><span data-stu-id="ea070-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="ea070-118">`ObjectPool`no ASP.NET Core 3,0 e posterior dá suporte ao `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="ea070-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="ea070-119">**Observação: o objectpool não coloca um limite no número de objetos que ele alocará, ele coloca um limite no número de objetos que será mantido.**</span><span class="sxs-lookup"><span data-stu-id="ea070-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="ea070-120">Conceitos</span><span class="sxs-lookup"><span data-stu-id="ea070-120">Concepts</span></span>

<span data-ttu-id="ea070-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-a abstração básica do pool de objetos.</span><span class="sxs-lookup"><span data-stu-id="ea070-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="ea070-122">Usado para obter e retornar objetos.</span><span class="sxs-lookup"><span data-stu-id="ea070-122">Used to get and return objects.</span></span>

<span data-ttu-id="ea070-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-Implemente isso para personalizar como um objeto é criado e como ele é *redefinido* quando retornado ao pool.</span><span class="sxs-lookup"><span data-stu-id="ea070-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="ea070-124">Isso pode ser passado para um pool de objetos que você constrói diretamente... OR</span><span class="sxs-lookup"><span data-stu-id="ea070-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="ea070-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>atua como um alocador para a criação de pools de objetos.</span><span class="sxs-lookup"><span data-stu-id="ea070-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="ea070-126">O objectpool pode ser usado em um aplicativo de várias maneiras:</span><span class="sxs-lookup"><span data-stu-id="ea070-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="ea070-127">Instanciando um pool.</span><span class="sxs-lookup"><span data-stu-id="ea070-127">Instantiating a pool.</span></span>
* <span data-ttu-id="ea070-128">Registrando um pool na [injeção de dependência](xref:fundamentals/dependency-injection) (di) como uma instância.</span><span class="sxs-lookup"><span data-stu-id="ea070-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="ea070-129">Registrar o `ObjectPoolProvider<>` em di e usá-lo como uma fábrica.</span><span class="sxs-lookup"><span data-stu-id="ea070-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="ea070-130">Como usar o objectpool</span><span class="sxs-lookup"><span data-stu-id="ea070-130">How to use ObjectPool</span></span>

<span data-ttu-id="ea070-131">Chame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> para obter um objeto e <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> retornar o objeto.</span><span class="sxs-lookup"><span data-stu-id="ea070-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="ea070-132">Não há nenhum requisito para que você retorne todos os objetos.</span><span class="sxs-lookup"><span data-stu-id="ea070-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="ea070-133">Se você não retornar um objeto, ele será coletado como lixo.</span><span class="sxs-lookup"><span data-stu-id="ea070-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="ea070-134">Quando <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> é usado e `T` implementa `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="ea070-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="ea070-135">Os itens que ***não*** forem retornados ao pool serão descartados.</span><span class="sxs-lookup"><span data-stu-id="ea070-135">Items that are ***not*** returned to the pool will be disposed.</span></span>
* <span data-ttu-id="ea070-136">Quando o pool é Descartado por DI, todos os itens no pool são descartados.</span><span class="sxs-lookup"><span data-stu-id="ea070-136">When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="ea070-137">Observação: depois que o pool é Descartado:</span><span class="sxs-lookup"><span data-stu-id="ea070-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="ea070-138">A chamada `Get` gera um `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="ea070-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="ea070-139">`return`descarta o item determinado.</span><span class="sxs-lookup"><span data-stu-id="ea070-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="ea070-140">Exemplo de objectpool</span><span class="sxs-lookup"><span data-stu-id="ea070-140">ObjectPool sample</span></span>

<span data-ttu-id="ea070-141">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ea070-141">The following code:</span></span>

* <span data-ttu-id="ea070-142">Adiciona `ObjectPoolProvider` ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ea070-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="ea070-143">Adiciona e configura `ObjectPool<StringBuilder>` o contêiner di.</span><span class="sxs-lookup"><span data-stu-id="ea070-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="ea070-144">Adiciona o `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="ea070-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="ea070-145">O código a seguir implementa`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="ea070-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
