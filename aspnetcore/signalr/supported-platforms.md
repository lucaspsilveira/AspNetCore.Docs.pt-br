---
title: ASP.NET Core SignalR plataformas com suporte
author: bradygaster
description: Saiba mais sobre as plataformas com suporte SignalRpara ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772599"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="54cf6-103">Plataformas com suporte do Signalr ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54cf6-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="54cf6-104">Requisitos do sistema do servidor do</span><span class="sxs-lookup"><span data-stu-id="54cf6-104">Server system requirements</span></span>

<span data-ttu-id="54cf6-105">O signalr para ASP.NET Core dá suporte a qualquer plataforma de servidor com suporte ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54cf6-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="54cf6-106">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="54cf6-106">JavaScript client</span></span>

<span data-ttu-id="54cf6-107">O [cliente JavaScript](xref:signalr/javascript-client) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="54cf6-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="54cf6-108">Navegador</span><span class="sxs-lookup"><span data-stu-id="54cf6-108">Browser</span></span>                         | <span data-ttu-id="54cf6-109">Versão</span><span class="sxs-lookup"><span data-stu-id="54cf6-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="54cf6-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="54cf6-110">Microsoft Edge</span></span>                  | <span data-ttu-id="54cf6-111">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="54cf6-111">Current&dagger;</span></span> |
| <span data-ttu-id="54cf6-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="54cf6-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="54cf6-113">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="54cf6-113">Current&dagger;</span></span> |
| <span data-ttu-id="54cf6-114">Google Chrome; inclui Android</span><span class="sxs-lookup"><span data-stu-id="54cf6-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="54cf6-115">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="54cf6-115">Current&dagger;</span></span> |
| <span data-ttu-id="54cf6-116">Safari inclui iOS</span><span class="sxs-lookup"><span data-stu-id="54cf6-116">Safari; includes iOS</span></span>            | <span data-ttu-id="54cf6-117">Atualizados&dagger;</span><span class="sxs-lookup"><span data-stu-id="54cf6-117">Current&dagger;</span></span> |
| <span data-ttu-id="54cf6-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="54cf6-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="54cf6-119">11</span><span class="sxs-lookup"><span data-stu-id="54cf6-119">11</span></span>              |

<span data-ttu-id="54cf6-120">&dagger;*Atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="54cf6-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="54cf6-121">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="54cf6-121">.NET client</span></span>

<span data-ttu-id="54cf6-122">O [cliente .net](xref:signalr/dotnet-client) é executado em qualquer plataforma com suporte do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54cf6-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="54cf6-123">Por exemplo, [os desenvolvedores do xamarin SignalR podem usar](https://github.com/aspnet/Announcements/issues/305) o para criar aplicativos Android usando xamarin. Android 8.4.0.1 e posterior e aplicativos Ios usando xamarin. Ios 11.14.0.4 e posterior.</span><span class="sxs-lookup"><span data-stu-id="54cf6-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="54cf6-124">Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="54cf6-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="54cf6-125">Outros transportes têm suporte em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="54cf6-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="54cf6-126">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="54cf6-126">Java client</span></span>

<span data-ttu-id="54cf6-127">O [cliente Java](xref:signalr/java-client) dá suporte a Java 8 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="54cf6-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="54cf6-128">Clientes sem suporte</span><span class="sxs-lookup"><span data-stu-id="54cf6-128">Unsupported clients</span></span>

<span data-ttu-id="54cf6-129">Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais.</span><span class="sxs-lookup"><span data-stu-id="54cf6-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="54cf6-130">Atualmente, eles não têm suporte e podem nunca ser.</span><span class="sxs-lookup"><span data-stu-id="54cf6-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="54cf6-131">[Cliente C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="54cf6-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="54cf6-132">[Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="54cf6-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
