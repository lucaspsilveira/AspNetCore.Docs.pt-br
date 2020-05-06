---
title: IP do cliente da assafe para ASP.NET Core
author: damienbod
description: Saiba como escrever filtros de middleware ou de ação para validar endereços IP remotos em uma lista de endereços IP aprovados.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776494"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="3dcfe-103">IP do cliente da assafe para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dcfe-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="3dcfe-104">Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3dcfe-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="3dcfe-105">Este artigo mostra três maneiras de implementar uma lista de permissões de endereço IP (também conhecida como List de permissão) em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="3dcfe-106">Um aplicativo de exemplo que o acompanha demonstra todas as três abordagens.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="3dcfe-107">Você pode usar:</span><span class="sxs-lookup"><span data-stu-id="3dcfe-107">You can use:</span></span>

* <span data-ttu-id="3dcfe-108">Middleware para verificar o endereço IP remoto de cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="3dcfe-109">Filtros de ação MVC para verificar o endereço IP remoto de solicitações para controladores ou métodos de ação específicos.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* Razor<span data-ttu-id="3dcfe-110">Páginas filtra para verificar o endereço IP remoto de solicitações de Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-110"> Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="3dcfe-111">Em cada caso, uma cadeia de caracteres contendo endereços IP de cliente aprovados é armazenada em uma configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="3dcfe-112">O middleware ou o filtro:</span><span class="sxs-lookup"><span data-stu-id="3dcfe-112">The middleware or filter:</span></span>

* <span data-ttu-id="3dcfe-113">Analisa a cadeia de caracteres em uma matriz.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="3dcfe-114">Verifica se o endereço IP remoto existe na matriz.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="3dcfe-115">O acesso será permitido se a matriz contiver o endereço IP.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="3dcfe-116">Caso contrário, um código de status HTTP 403 proibido será retornado.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="3dcfe-117">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3dcfe-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="3dcfe-118">Endereço IP do SafeList</span><span class="sxs-lookup"><span data-stu-id="3dcfe-118">IP address safelist</span></span>

<span data-ttu-id="3dcfe-119">No aplicativo de exemplo, a SafeList de endereço IP é:</span><span class="sxs-lookup"><span data-stu-id="3dcfe-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="3dcfe-120">Definido pela `AdminSafeList` Propriedade no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3dcfe-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="3dcfe-121">Uma cadeia de caracteres delimitada por ponto-e-vírgula que pode conter endereços IP [versão 4 (IPv4)](https://wikipedia.org/wiki/IPv4) e [protocolo IP versão 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .</span><span class="sxs-lookup"><span data-stu-id="3dcfe-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="3dcfe-122">No exemplo anterior, os endereços IPv4 `127.0.0.1` de e `192.168.1.5` e o endereço de loopback IPv6 de `::1` (formato compactado `0:0:0:0:0:0:0:1`) são permitidos.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="3dcfe-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="3dcfe-123">Middleware</span></span>

<span data-ttu-id="3dcfe-124">O `Startup.Configure` método adiciona o tipo `AdminSafeListMiddleware` de middleware personalizado ao pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="3dcfe-125">A SafeList é recuperada com o provedor de configuração do .NET Core e é passada como um parâmetro de construtor.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="3dcfe-126">O middleware analisa a cadeia de caracteres em uma matriz e procura o endereço IP remoto na matriz.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="3dcfe-127">Se o endereço IP remoto não for encontrado, o middleware retornará HTTP 403 Proibido.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="3dcfe-128">Esse processo de validação é ignorado para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="3dcfe-129">Filtro de ação</span><span class="sxs-lookup"><span data-stu-id="3dcfe-129">Action filter</span></span>

<span data-ttu-id="3dcfe-130">Se você quiser o controle de acesso controlado por forma segura para controladores ou métodos de ação específicos do MVC, use um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="3dcfe-131">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="3dcfe-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="3dcfe-132">No `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros do MVC.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="3dcfe-133">No exemplo a seguir, um `ClientIpCheckActionFilter` filtro de ação é adicionado.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="3dcfe-134">Uma lista de imsafe e uma instância de agente de console são passadas como parâmetros de construtor.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="3dcfe-135">O filtro de ação pode então ser aplicado a um controlador ou método de ação com o atributo [[userFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="3dcfe-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="3dcfe-136">No aplicativo de exemplo, o filtro de ação é aplicado ao método de `Get` ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="3dcfe-137">Ao testar o aplicativo enviando:</span><span class="sxs-lookup"><span data-stu-id="3dcfe-137">When you test the app by sending:</span></span>

* <span data-ttu-id="3dcfe-138">Uma solicitação HTTP GET, o `[ServiceFilter]` atributo valida o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="3dcfe-139">Se o acesso for permitido para `Get` o método de ação, uma variação da seguinte saída do console será produzida pelo filtro de ação e pelo método de ação:</span><span class="sxs-lookup"><span data-stu-id="3dcfe-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="3dcfe-140">Um verbo de solicitação HTTP diferente de GET, `AdminSafeListMiddleware` o middleware valida o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a>Razor<span data-ttu-id="3dcfe-141">Filtro de páginas</span><span class="sxs-lookup"><span data-stu-id="3dcfe-141"> Pages filter</span></span>

<span data-ttu-id="3dcfe-142">Se você quiser o controle de acesso controlado por seguração para um Razor aplicativo de Razor páginas, use um filtro de páginas.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="3dcfe-143">Por exemplo: </span><span class="sxs-lookup"><span data-stu-id="3dcfe-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="3dcfe-144">No `Startup.ConfigureServices`, habilite Razor o filtro páginas adicionando-o à coleção de filtros MVC.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="3dcfe-145">No exemplo a seguir, um `ClientIpCheckPageFilter` Razor filtro de páginas é adicionado.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="3dcfe-146">Uma lista de imsafe e uma instância de agente de console são passadas como parâmetros de construtor.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="3dcfe-147">Quando a página de *índice* Razor do aplicativo de exemplo é solicitada, o filtro de Razor páginas valida o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="3dcfe-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="3dcfe-148">O filtro produz uma variação da seguinte saída do console:</span><span class="sxs-lookup"><span data-stu-id="3dcfe-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="3dcfe-149">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3dcfe-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="3dcfe-150">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="3dcfe-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
