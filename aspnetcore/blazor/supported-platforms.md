---
title: plataformas Blazor suportadas pelo ASP.NET Core
author: guardrex
description: Conheça as plataformas suportadas Blazorpara ASP.NET Core .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658854"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="44319-103">ASP.NET Plataformas suportadas pelo Core Blazor</span><span class="sxs-lookup"><span data-stu-id="44319-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="44319-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="44319-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="44319-105">Requisitos de navegador</span><span class="sxs-lookup"><span data-stu-id="44319-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="44319-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="44319-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="44319-107">Navegador</span><span class="sxs-lookup"><span data-stu-id="44319-107">Browser</span></span>                          | <span data-ttu-id="44319-108">Versão</span><span class="sxs-lookup"><span data-stu-id="44319-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="44319-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44319-109">Microsoft Edge</span></span>                   | <span data-ttu-id="44319-110">Current</span><span class="sxs-lookup"><span data-stu-id="44319-110">Current</span></span>               |
| <span data-ttu-id="44319-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44319-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="44319-112">Current</span><span class="sxs-lookup"><span data-stu-id="44319-112">Current</span></span>               |
| <span data-ttu-id="44319-113">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="44319-113">Google Chrome, including Android</span></span> | <span data-ttu-id="44319-114">Current</span><span class="sxs-lookup"><span data-stu-id="44319-114">Current</span></span>               |
| <span data-ttu-id="44319-115">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="44319-115">Safari, including iOS</span></span>            | <span data-ttu-id="44319-116">Current</span><span class="sxs-lookup"><span data-stu-id="44319-116">Current</span></span>               |
| <span data-ttu-id="44319-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44319-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="44319-118">Não suportado&dagger;</span><span class="sxs-lookup"><span data-stu-id="44319-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="44319-119">&dagger;O Microsoft Internet Explorer não suporta [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="44319-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="44319-120">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="44319-120">Blazor Server</span></span>

| <span data-ttu-id="44319-121">Navegador</span><span class="sxs-lookup"><span data-stu-id="44319-121">Browser</span></span>                          | <span data-ttu-id="44319-122">Versão</span><span class="sxs-lookup"><span data-stu-id="44319-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="44319-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44319-123">Microsoft Edge</span></span>                   | <span data-ttu-id="44319-124">Current</span><span class="sxs-lookup"><span data-stu-id="44319-124">Current</span></span>    |
| <span data-ttu-id="44319-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44319-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="44319-126">Current</span><span class="sxs-lookup"><span data-stu-id="44319-126">Current</span></span>    |
| <span data-ttu-id="44319-127">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="44319-127">Google Chrome, including Android</span></span> | <span data-ttu-id="44319-128">Current</span><span class="sxs-lookup"><span data-stu-id="44319-128">Current</span></span>    |
| <span data-ttu-id="44319-129">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="44319-129">Safari, including iOS</span></span>            | <span data-ttu-id="44319-130">Current</span><span class="sxs-lookup"><span data-stu-id="44319-130">Current</span></span>    |
| <span data-ttu-id="44319-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44319-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="44319-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="44319-132">11&dagger;</span></span> |

<span data-ttu-id="44319-133">&dagger;São necessários polipreenchimentos adicionais (por exemplo, promessas podem ser adicionadas através de um pacote [de Polyfill.io).](https://polyfill.io/v3/)</span><span class="sxs-lookup"><span data-stu-id="44319-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44319-134">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="44319-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
