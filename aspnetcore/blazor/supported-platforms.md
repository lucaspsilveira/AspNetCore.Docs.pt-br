---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401929"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="e5d36-103">ASP.NET Core Blazor plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="e5d36-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="e5d36-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e5d36-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="e5d36-105">Requisitos de navegador</span><span class="sxs-lookup"><span data-stu-id="e5d36-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="e5d36-106">Navegador</span><span class="sxs-lookup"><span data-stu-id="e5d36-106">Browser</span></span>                          | <span data-ttu-id="e5d36-107">Versão</span><span class="sxs-lookup"><span data-stu-id="e5d36-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="e5d36-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e5d36-108">Microsoft Edge</span></span>                   | <span data-ttu-id="e5d36-109">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-109">Current</span></span>               |
| <span data-ttu-id="e5d36-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e5d36-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="e5d36-111">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-111">Current</span></span>               |
| <span data-ttu-id="e5d36-112">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="e5d36-112">Google Chrome, including Android</span></span> | <span data-ttu-id="e5d36-113">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-113">Current</span></span>               |
| <span data-ttu-id="e5d36-114">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="e5d36-114">Safari, including iOS</span></span>            | <span data-ttu-id="e5d36-115">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-115">Current</span></span>               |
| <span data-ttu-id="e5d36-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e5d36-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e5d36-117">Sem suporte&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5d36-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="e5d36-118">&dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="e5d36-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="e5d36-119">Navegador</span><span class="sxs-lookup"><span data-stu-id="e5d36-119">Browser</span></span>                          | <span data-ttu-id="e5d36-120">Versão</span><span class="sxs-lookup"><span data-stu-id="e5d36-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="e5d36-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e5d36-121">Microsoft Edge</span></span>                   | <span data-ttu-id="e5d36-122">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-122">Current</span></span>    |
| <span data-ttu-id="e5d36-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e5d36-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="e5d36-124">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-124">Current</span></span>    |
| <span data-ttu-id="e5d36-125">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="e5d36-125">Google Chrome, including Android</span></span> | <span data-ttu-id="e5d36-126">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-126">Current</span></span>    |
| <span data-ttu-id="e5d36-127">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="e5d36-127">Safari, including iOS</span></span>            | <span data-ttu-id="e5d36-128">Current</span><span class="sxs-lookup"><span data-stu-id="e5d36-128">Current</span></span>    |
| <span data-ttu-id="e5d36-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e5d36-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e5d36-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="e5d36-130">11&dagger;</span></span> |

<span data-ttu-id="e5d36-131">&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote).</span><span class="sxs-lookup"><span data-stu-id="e5d36-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5d36-132">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e5d36-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
