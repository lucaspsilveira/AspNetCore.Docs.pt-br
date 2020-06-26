---
title: ASP.NET Core SignalR plataformas com suporte
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9d7dd946ae5cf83b76a83ba1faeed0bb3fc31cef
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405816"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="6419f-103">ASP.NET Core SignalR plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="6419f-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="6419f-104">Requisitos do sistema do servidor do</span><span class="sxs-lookup"><span data-stu-id="6419f-104">Server system requirements</span></span>

SignalR<span data-ttu-id="6419f-105">para ASP.NET Core dá suporte a qualquer plataforma de servidor que ASP.NET Core suporte.</span><span class="sxs-lookup"><span data-stu-id="6419f-105"> for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="6419f-106">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="6419f-106">JavaScript client</span></span>

<span data-ttu-id="6419f-107">O [cliente JavaScript](xref:signalr/javascript-client) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="6419f-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="6419f-108">Navegador</span><span class="sxs-lookup"><span data-stu-id="6419f-108">Browser</span></span>                         | <span data-ttu-id="6419f-109">Versão</span><span class="sxs-lookup"><span data-stu-id="6419f-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="6419f-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="6419f-110">Microsoft Edge</span></span>                  | <span data-ttu-id="6419f-111">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6419f-111">Current&dagger;</span></span> |
| <span data-ttu-id="6419f-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6419f-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="6419f-113">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6419f-113">Current&dagger;</span></span> |
| <span data-ttu-id="6419f-114">Google Chrome; inclui Android</span><span class="sxs-lookup"><span data-stu-id="6419f-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="6419f-115">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6419f-115">Current&dagger;</span></span> |
| <span data-ttu-id="6419f-116">Safari inclui iOS</span><span class="sxs-lookup"><span data-stu-id="6419f-116">Safari; includes iOS</span></span>            | <span data-ttu-id="6419f-117">Current&dagger;</span><span class="sxs-lookup"><span data-stu-id="6419f-117">Current&dagger;</span></span> |
| <span data-ttu-id="6419f-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6419f-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="6419f-119">11</span><span class="sxs-lookup"><span data-stu-id="6419f-119">11</span></span>              |

<span data-ttu-id="6419f-120">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="6419f-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="6419f-121">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="6419f-121">.NET client</span></span>

<span data-ttu-id="6419f-122">O [cliente .net](xref:signalr/dotnet-client) é executado em qualquer plataforma com suporte do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6419f-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="6419f-123">Por exemplo, [os desenvolvedores do xamarin SignalR podem usar](https://github.com/aspnet/Announcements/issues/305) o para criar aplicativos Android usando xamarin. Android 8.4.0.1 e posterior e aplicativos Ios usando xamarin. Ios 11.14.0.4 e posterior.</span><span class="sxs-lookup"><span data-stu-id="6419f-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="6419f-124">Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="6419f-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="6419f-125">Outros transportes têm suporte em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="6419f-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="6419f-126">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="6419f-126">Java client</span></span>

<span data-ttu-id="6419f-127">O [cliente Java](xref:signalr/java-client) dá suporte a Java 8 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="6419f-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="6419f-128">Clientes sem suporte</span><span class="sxs-lookup"><span data-stu-id="6419f-128">Unsupported clients</span></span>

<span data-ttu-id="6419f-129">Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais.</span><span class="sxs-lookup"><span data-stu-id="6419f-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="6419f-130">Atualmente, eles não têm suporte e podem nunca ser.</span><span class="sxs-lookup"><span data-stu-id="6419f-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="6419f-131">[Cliente C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="6419f-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="6419f-132">[Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="6419f-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
