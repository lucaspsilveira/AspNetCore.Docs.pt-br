---
title: Lista de segurança ip do cliente para ASP.NET Core
author: damienbod
description: Aprenda a escrever middleware ou filtros de ação para validar endereços IP remotos em uma lista de endereços IP aprovados.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471805"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="175ce-103">Lista de segurança ip do cliente para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="175ce-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="175ce-104">Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="175ce-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="175ce-105">Este artigo mostra três maneiras de implementar uma lista de segurança de endereço IP (também conhecida como lista de permitir) em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="175ce-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="175ce-106">Um aplicativo de amostragem que acompanha demonstra as três abordagens.</span><span class="sxs-lookup"><span data-stu-id="175ce-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="175ce-107">Você pode usar:</span><span class="sxs-lookup"><span data-stu-id="175ce-107">You can use:</span></span>

* <span data-ttu-id="175ce-108">Middleware para verificar o endereço IP remoto de cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="175ce-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="175ce-109">Os filtros de ação MVC para verificar o endereço IP remoto das solicitações de controladores específicos ou métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="175ce-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="175ce-110">Os filtros Razor Pages para verificar o endereço IP remoto das solicitações de páginas de Navalha.</span><span class="sxs-lookup"><span data-stu-id="175ce-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="175ce-111">Em cada caso, uma seqüência contendo endereços IP do cliente aprovados é armazenada em uma configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="175ce-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="175ce-112">O middleware ou filtro:</span><span class="sxs-lookup"><span data-stu-id="175ce-112">The middleware or filter:</span></span>

* <span data-ttu-id="175ce-113">Analisa a seqüência em uma matriz.</span><span class="sxs-lookup"><span data-stu-id="175ce-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="175ce-114">Verifica se o endereço IP remoto existe na matriz.</span><span class="sxs-lookup"><span data-stu-id="175ce-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="175ce-115">O acesso é permitido se a matriz contiver o endereço IP.</span><span class="sxs-lookup"><span data-stu-id="175ce-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="175ce-116">Caso contrário, um código de status proibido HTTP 403 é devolvido.</span><span class="sxs-lookup"><span data-stu-id="175ce-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="175ce-117">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="175ce-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="175ce-118">Lista de segurança de endereçoIP</span><span class="sxs-lookup"><span data-stu-id="175ce-118">IP address safelist</span></span>

<span data-ttu-id="175ce-119">No aplicativo de exemplo, a lista de segurança de endereço IP é:</span><span class="sxs-lookup"><span data-stu-id="175ce-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="175ce-120">Definido pela `AdminSafeList` propriedade no arquivo *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="175ce-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="175ce-121">Uma seqüência delimitada em ponto e vírgula que pode conter endereços da [versão 4 do Protocolo da Internet (IPv4)](https://wikipedia.org/wiki/IPv4) e do Protocolo da Internet versão 6 [(IPv6).](https://wikipedia.org/wiki/IPv6)</span><span class="sxs-lookup"><span data-stu-id="175ce-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="175ce-122">No exemplo anterior, os endereços `127.0.0.1` `192.168.1.5` IPv4 e o endereço `::1` de loopback `0:0:0:0:0:0:0:1`IPv6 (formato compactado para) são permitidos.</span><span class="sxs-lookup"><span data-stu-id="175ce-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="175ce-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="175ce-123">Middleware</span></span>

<span data-ttu-id="175ce-124">O `Startup.Configure` método adiciona `AdminSafeListMiddleware` o tipo de middleware personalizado ao pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="175ce-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="175ce-125">A lista de segurança é recuperada com o provedor de configuração .NET Core e é passada como um parâmetro de construção.</span><span class="sxs-lookup"><span data-stu-id="175ce-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="175ce-126">O middleware analisa a seqüência em uma matriz e procura o endereço IP remoto na matriz.</span><span class="sxs-lookup"><span data-stu-id="175ce-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="175ce-127">Se o endereço IP remoto não for encontrado, o middleware retorna HTTP 403 Proibido.</span><span class="sxs-lookup"><span data-stu-id="175ce-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="175ce-128">Este processo de validação é ignorado para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="175ce-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="175ce-129">Filtro de ação</span><span class="sxs-lookup"><span data-stu-id="175ce-129">Action filter</span></span>

<span data-ttu-id="175ce-130">Se você quiser um controle de acesso orientado por listas seguras para controladores MVC específicos ou métodos de ação, use um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="175ce-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="175ce-131">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="175ce-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="175ce-132">Em `Startup.ConfigureServices`, adicione o filtro de ação à coleção de filtros MVC.</span><span class="sxs-lookup"><span data-stu-id="175ce-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="175ce-133">No exemplo a `ClientIpCheckActionFilter` seguir, um filtro de ação é adicionado.</span><span class="sxs-lookup"><span data-stu-id="175ce-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="175ce-134">Uma lista de segurança e uma instância de logger do console são passadas como parâmetros de construção.</span><span class="sxs-lookup"><span data-stu-id="175ce-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="175ce-135">O filtro de ação pode então ser aplicado a um controlador ou método de ação com o atributo [[ServiceFilter]:](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)</span><span class="sxs-lookup"><span data-stu-id="175ce-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="175ce-136">No aplicativo de amostra, o filtro de ação `Get` é aplicado ao método de ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="175ce-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="175ce-137">Quando você testa o aplicativo enviando:</span><span class="sxs-lookup"><span data-stu-id="175ce-137">When you test the app by sending:</span></span>

* <span data-ttu-id="175ce-138">Uma solicitação HTTP `[ServiceFilter]` GET, o atributo valida o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="175ce-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="175ce-139">Se o `Get` acesso for permitido ao método de ação, uma variação da seguinte saída do console é produzida pelo filtro de ação e pelo método de ação:</span><span class="sxs-lookup"><span data-stu-id="175ce-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="175ce-140">Um verbo de solicitação HTTP `AdminSafeListMiddleware` que não seja GET, o middleware valida o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="175ce-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="175ce-141">Filtro de páginas de barbear</span><span class="sxs-lookup"><span data-stu-id="175ce-141">Razor Pages filter</span></span>

<span data-ttu-id="175ce-142">Se você quiser um controle de acesso orientado por listas seguras para um aplicativo Razor Pages, use um filtro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="175ce-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="175ce-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="175ce-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="175ce-144">In `Startup.ConfigureServices`, habilite o filtro Páginas de Navalha adicionando-o à coleção de filtros MVC.</span><span class="sxs-lookup"><span data-stu-id="175ce-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="175ce-145">No exemplo a `ClientIpCheckPageFilter` seguir, um filtro Páginas de Navalha é adicionado.</span><span class="sxs-lookup"><span data-stu-id="175ce-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="175ce-146">Uma lista de segurança e uma instância de logger do console são passadas como parâmetros de construção.</span><span class="sxs-lookup"><span data-stu-id="175ce-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="175ce-147">Quando a página *Index* Razor do aplicativo de amostra é solicitada, o filtro Páginas de Barbear valida o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="175ce-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="175ce-148">O filtro produz uma variação da seguinte saída do console:</span><span class="sxs-lookup"><span data-stu-id="175ce-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="175ce-149">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="175ce-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="175ce-150">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="175ce-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
