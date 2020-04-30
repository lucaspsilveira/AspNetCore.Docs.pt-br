---
title: Introdução ao ASP.NET Core
author: rick-anderson
description: Obtenha uma introdução ao ASP.NET Core, uma estrutura de software livre de várias plataformas e de alto desempenho para a criação de aplicativos modernos conectados à Internet, em nuvem.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: d70998d0149a0cc7018946a93ab63b5e10b76b78
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558941"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="8186c-103">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8186c-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="8186c-104">Por [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="8186c-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8186c-105">ASP.NET Core é uma estrutura de software livre de plataforma cruzada, de alto desempenho [para a criação](https://github.com/dotnet/aspnetcore) de aplicativos modernos conectados à Internet, em nuvem.</span><span class="sxs-lookup"><span data-stu-id="8186c-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="8186c-106">Com o ASP.NET Core, você pode:</span><span class="sxs-lookup"><span data-stu-id="8186c-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="8186c-107">Crie aplicativos Web e serviços, [Internet das coisas (IOT)](https://www.microsoft.com/internet-of-things/) e back-ends móveis.</span><span class="sxs-lookup"><span data-stu-id="8186c-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="8186c-108">Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="8186c-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="8186c-109">Implantar na nuvem ou local.</span><span class="sxs-lookup"><span data-stu-id="8186c-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="8186c-110">Executar no [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="8186c-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="8186c-111">Por que escolher o ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="8186c-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="8186c-112">Milhões de desenvolvedores usam ou usaram o [ASP.NET 4. x](/aspnet/overview) para criar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="8186c-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="8186c-113">ASP.NET Core é um redesign de ASP.NET 4. x, incluindo alterações arquitetônicas que resultam em uma estrutura mais Lean e mais modular.</span><span class="sxs-lookup"><span data-stu-id="8186c-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="8186c-114">Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="8186c-115">O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="8186c-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="8186c-116">O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.</span><span class="sxs-lookup"><span data-stu-id="8186c-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="8186c-117">[Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna a criação da interface do usuário da Web mais fácil e mais produtiva.</span><span class="sxs-lookup"><span data-stu-id="8186c-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="8186c-118">A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="8186c-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="8186c-119">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="8186c-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="8186c-120">O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="8186c-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="8186c-121">O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="8186c-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="8186c-122">A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="8186c-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="8186c-123">Desenvolvimento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="8186c-123">Client-side development</span></span>

<span data-ttu-id="8186c-124">O ASP.NET Core integra-se perfeitamente com estruturas conhecidas do lado do cliente e bibliotecas, incluindo [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="8186c-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="8186c-125">Para obter mais informações, confira <xref:blazor/index> e os tópicos relacionados em *Desenvolvimento do lado do cliente*.</span><span class="sxs-lookup"><span data-stu-id="8186c-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="8186c-126">ASP.NET Core estruturas de destino</span><span class="sxs-lookup"><span data-stu-id="8186c-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="8186c-127">ASP.NET Core 3. x e posterior só podem ter como destino o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8186c-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="8186c-128">Em geral, ASP.NET Core é composta de bibliotecas de [.net Standard](/dotnet/standard/net-standard) .</span><span class="sxs-lookup"><span data-stu-id="8186c-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="8186c-129">As bibliotecas gravadas com .NET Standard 2.0 podem ser executadas em qualquer [plataforma .NET que implemente o .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="8186c-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="8186c-130">Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão.</span><span class="sxs-lookup"><span data-stu-id="8186c-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="8186c-131">Algumas vantagens do .NET Core em relação ao .NET Framework incluem:</span><span class="sxs-lookup"><span data-stu-id="8186c-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="8186c-132">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="8186c-132">Cross-platform.</span></span> <span data-ttu-id="8186c-133">É executado no Windows, no macOS e no Linux.</span><span class="sxs-lookup"><span data-stu-id="8186c-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="8186c-134">desempenho aprimorado</span><span class="sxs-lookup"><span data-stu-id="8186c-134">Improved performance</span></span>
* [<span data-ttu-id="8186c-135">Controle de versão lado a lado</span><span class="sxs-lookup"><span data-stu-id="8186c-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="8186c-136">Novas APIs</span><span class="sxs-lookup"><span data-stu-id="8186c-136">New APIs</span></span>
* <span data-ttu-id="8186c-137">Código-fonte aberto</span><span class="sxs-lookup"><span data-stu-id="8186c-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="8186c-138">Caminho de aprendizado recomendado</span><span class="sxs-lookup"><span data-stu-id="8186c-138">Recommended learning path</span></span>

<span data-ttu-id="8186c-139">Recomendamos a seguinte sequência de tutoriais para uma introdução ao desenvolvimento de ASP.NET Core aplicativos:</span><span class="sxs-lookup"><span data-stu-id="8186c-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="8186c-140">Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter.</span><span class="sxs-lookup"><span data-stu-id="8186c-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="8186c-141">Tipo de aplicativo</span><span class="sxs-lookup"><span data-stu-id="8186c-141">App type</span></span>  |<span data-ttu-id="8186c-142">Cenário</span><span class="sxs-lookup"><span data-stu-id="8186c-142">Scenario</span></span>  |<span data-ttu-id="8186c-143">Tutorial</span><span class="sxs-lookup"><span data-stu-id="8186c-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="8186c-144">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8186c-144">Web app</span></span>                   | <span data-ttu-id="8186c-145">Novo desenvolvimento de interface do usuário na Web do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="8186c-145">New server-side web UI development</span></span> |[<span data-ttu-id="8186c-146">Introdução a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8186c-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="8186c-147">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8186c-147">Web app</span></span>                   | <span data-ttu-id="8186c-148">Mantendo um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="8186c-149">Introdução ao MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="8186c-150">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8186c-150">Web app</span></span>                   | <span data-ttu-id="8186c-151">Desenvolvimento de interface do usuário na Web do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="8186c-151">Client-side web UI development</span></span> |[<span data-ttu-id="8186c-152">Introdução ao mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="8186c-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="8186c-153">API Web</span><span class="sxs-lookup"><span data-stu-id="8186c-153">Web API</span></span>                   | <span data-ttu-id="8186c-154">Serviços HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="8186c-154">RESTful HTTP services</span></span> |<span data-ttu-id="8186c-155">[Criar uma API Web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="8186c-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="8186c-156">Aplicativo de chamada de procedimento remoto</span><span class="sxs-lookup"><span data-stu-id="8186c-156">Remote Procedure Call app</span></span> | <span data-ttu-id="8186c-157">Serviços de primeiro contrato usando buffers de protocolo</span><span class="sxs-lookup"><span data-stu-id="8186c-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="8186c-158">Introdução a um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="8186c-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="8186c-159">Aplicativo em tempo real</span><span class="sxs-lookup"><span data-stu-id="8186c-159">Real-time app</span></span>             | <span data-ttu-id="8186c-160">Comunicação bidirecional entre servidores e clientes conectados</span><span class="sxs-lookup"><span data-stu-id="8186c-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="8186c-161">Introdução ao SignalR</span><span class="sxs-lookup"><span data-stu-id="8186c-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="8186c-162">Siga um tutorial que mostra como fazer o acesso a dados básico.</span><span class="sxs-lookup"><span data-stu-id="8186c-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="8186c-163">Cenário</span><span class="sxs-lookup"><span data-stu-id="8186c-163">Scenario</span></span>  |<span data-ttu-id="8186c-164">Tutorial</span><span class="sxs-lookup"><span data-stu-id="8186c-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="8186c-165">Novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="8186c-165">New development</span></span>        |[<span data-ttu-id="8186c-166">Razor Pages com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8186c-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="8186c-167">Mantendo um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="8186c-168">MVC com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8186c-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="8186c-169">Leia uma visão geral de ASP.NET Core [conceitos básicos](xref:fundamentals/index) que se aplicam a todos os tipos de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="8186c-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="8186c-170">Procure outros tópicos de interesse no sumário.</span><span class="sxs-lookup"><span data-stu-id="8186c-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="8186c-171">&dagger;Há também um [tutorial de API Web interativa](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="8186c-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="8186c-172">Nenhuma instalação local de ferramentas de desenvolvimento é necessária.</span><span class="sxs-lookup"><span data-stu-id="8186c-172">No local installation of development tools is required.</span></span> <span data-ttu-id="8186c-173">O código é executado em um [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no navegador, e a [ondulação](https://curl.haxx.se/) é usada para teste.</span><span class="sxs-lookup"><span data-stu-id="8186c-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="8186c-174">Migrar do .NET Framework</span><span class="sxs-lookup"><span data-stu-id="8186c-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="8186c-175">Para obter um guia de referência para migrar aplicativos ASP.NET 4. x para <xref:migration/proper-to-2x/index>ASP.NET Core, consulte.</span><span class="sxs-lookup"><span data-stu-id="8186c-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8186c-176">ASP.NET Core é uma estrutura de software livre de plataforma cruzada, de alto desempenho [para a criação](https://github.com/dotnet/aspnetcore) de aplicativos modernos conectados à Internet, em nuvem.</span><span class="sxs-lookup"><span data-stu-id="8186c-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="8186c-177">Com o ASP.NET Core, você pode:</span><span class="sxs-lookup"><span data-stu-id="8186c-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="8186c-178">Crie aplicativos Web e serviços, [Internet das coisas (IOT)](https://www.microsoft.com/internet-of-things/) e back-ends móveis.</span><span class="sxs-lookup"><span data-stu-id="8186c-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="8186c-179">Usar suas ferramentas de desenvolvimento favoritas no Windows, macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="8186c-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="8186c-180">Implantar na nuvem ou local.</span><span class="sxs-lookup"><span data-stu-id="8186c-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="8186c-181">Executar no [.NET Core ou no .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="8186c-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="8186c-182">Por que escolher o ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="8186c-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="8186c-183">Milhões de desenvolvedores usam ou usaram o [ASP.NET 4. x](/aspnet/overview) para criar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="8186c-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="8186c-184">O ASP.NET Core é uma reformulação do ASP.NET 4.x, com alterações de arquitetura que resultam em uma estrutura mais enxuta e modular.</span><span class="sxs-lookup"><span data-stu-id="8186c-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="8186c-185">Compilar APIs Web e uma interface do usuário da Web usando o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="8186c-186">O ASP.NET Core MVC fornece recursos que ajudam você a compilar [APIs Web](xref:tutorials/first-web-api) e [aplicativos Web](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="8186c-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="8186c-187">O [padrão MVC (Model-View-Controller)](xref:mvc/overview) ajuda a tornar as APIs Web e os aplicativos Web testáveis.</span><span class="sxs-lookup"><span data-stu-id="8186c-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="8186c-188">[Razor Pages](xref:razor-pages/index) é um modelo de programação baseado em página que torna a criação da interface do usuário da Web mais fácil e mais produtiva.</span><span class="sxs-lookup"><span data-stu-id="8186c-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="8186c-189">A [marcação Razor](xref:mvc/views/razor) fornece uma sintaxe produtiva para [Páginas Razor](xref:razor-pages/index) e as [Exibições do MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="8186c-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="8186c-190">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="8186c-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="8186c-191">O suporte interno para [vários formatos de dados e negociação de conteúdo](xref:web-api/advanced/formatting) permite que as APIs Web alcancem uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.</span><span class="sxs-lookup"><span data-stu-id="8186c-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="8186c-192">O [model binding](xref:mvc/models/model-binding) mapeia automaticamente os dados de solicitações HTTP para os parâmetros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="8186c-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="8186c-193">A [Validação de Modelos](xref:mvc/models/validation) executa automaticamente a validação no lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="8186c-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="8186c-194">Desenvolvimento do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="8186c-194">Client-side development</span></span>

<span data-ttu-id="8186c-195">O ASP.NET Core integra-se perfeitamente com estruturas conhecidas do lado do cliente e bibliotecas, incluindo [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) e [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="8186c-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="8186c-196">Para obter mais informações, confira <xref:blazor/index> e os tópicos relacionados em *Desenvolvimento do lado do cliente*.</span><span class="sxs-lookup"><span data-stu-id="8186c-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="8186c-197">ASP.NET Core direcionado para o .NET Framework</span><span class="sxs-lookup"><span data-stu-id="8186c-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="8186c-198">O ASP.NET Core 2.x pode ser direcionado para o .NET Core ou ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8186c-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="8186c-199">Os aplicativos do ASP.NET Core direcionados ao .NET Framework não são multiplataforma,&mdash; são executados somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="8186c-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="8186c-200">Em geral, o ASP.NET Core 2.x é composto de bibliotecas do [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="8186c-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="8186c-201">As bibliotecas gravadas com .NET Standard 2.0 podem ser executadas em qualquer [plataforma .NET que implemente o .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="8186c-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="8186c-202">O ASP.NET Core 2.x dá suporte para as versões do .NET Framework que implementam o .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="8186c-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="8186c-203">.NET Framework versão mais recente é recomendada.</span><span class="sxs-lookup"><span data-stu-id="8186c-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="8186c-204">.NET Framework 4.6.1 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="8186c-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="8186c-205">O ASP.NET Core 3.0 e posterior somente executará no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8186c-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="8186c-206">Para obter mais detalhes sobre essa alteração, confira [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Uma primeira análise das alterações no ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="8186c-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="8186c-207">Há várias vantagens em direcionar para o .NET Core, e essas vantagens aumentam com cada versão.</span><span class="sxs-lookup"><span data-stu-id="8186c-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="8186c-208">Algumas vantagens do .NET Core em relação ao .NET Framework incluem:</span><span class="sxs-lookup"><span data-stu-id="8186c-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="8186c-209">Multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="8186c-209">Cross-platform.</span></span> <span data-ttu-id="8186c-210">É executado no Windows, no Linux e no macOS.</span><span class="sxs-lookup"><span data-stu-id="8186c-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="8186c-211">desempenho aprimorado</span><span class="sxs-lookup"><span data-stu-id="8186c-211">Improved performance</span></span>
* [<span data-ttu-id="8186c-212">Controle de versão lado a lado</span><span class="sxs-lookup"><span data-stu-id="8186c-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="8186c-213">Novas APIs</span><span class="sxs-lookup"><span data-stu-id="8186c-213">New APIs</span></span>
* <span data-ttu-id="8186c-214">Código-fonte aberto</span><span class="sxs-lookup"><span data-stu-id="8186c-214">Open source</span></span>

<span data-ttu-id="8186c-215">Para ajudar a fechar a lacuna da API do .NET Framework para o .NET Core, o [pacote de compatibilidade do Windows](/dotnet/core/porting/windows-compat-pack) tornou milhares de APIs somente do Windows disponíveis no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8186c-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="8186c-216">Essas APIs não estavam disponíveis no .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="8186c-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="8186c-217">Caminho de aprendizado recomendado</span><span class="sxs-lookup"><span data-stu-id="8186c-217">Recommended learning path</span></span>

<span data-ttu-id="8186c-218">Recomendamos a seguinte sequência de tutoriais e artigos para obter uma introdução ao desenvolvimento de aplicativos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8186c-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="8186c-219">Siga um tutorial para o tipo de aplicativo que você deseja desenvolver ou manter.</span><span class="sxs-lookup"><span data-stu-id="8186c-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="8186c-220">Tipo de aplicativo</span><span class="sxs-lookup"><span data-stu-id="8186c-220">App type</span></span>  |<span data-ttu-id="8186c-221">Cenário</span><span class="sxs-lookup"><span data-stu-id="8186c-221">Scenario</span></span>  |<span data-ttu-id="8186c-222">Tutorial</span><span class="sxs-lookup"><span data-stu-id="8186c-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="8186c-223">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8186c-223">Web app</span></span>                   | <span data-ttu-id="8186c-224">Para novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="8186c-224">For new development</span></span>        |[<span data-ttu-id="8186c-225">Introdução a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8186c-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="8186c-226">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="8186c-226">Web app</span></span>                   | <span data-ttu-id="8186c-227">Para manter um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="8186c-228">Introdução ao MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="8186c-229">API Web</span><span class="sxs-lookup"><span data-stu-id="8186c-229">Web API</span></span>                   |                            |<span data-ttu-id="8186c-230">[Criar uma API Web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="8186c-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="8186c-231">Aplicativo em tempo real</span><span class="sxs-lookup"><span data-stu-id="8186c-231">Real-time app</span></span>             |                            |[<span data-ttu-id="8186c-232">Introdução ao SignalR</span><span class="sxs-lookup"><span data-stu-id="8186c-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="8186c-233">Siga um tutorial que mostra como fazer o acesso a dados básico.</span><span class="sxs-lookup"><span data-stu-id="8186c-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="8186c-234">Cenário</span><span class="sxs-lookup"><span data-stu-id="8186c-234">Scenario</span></span>  |<span data-ttu-id="8186c-235">Tutorial</span><span class="sxs-lookup"><span data-stu-id="8186c-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="8186c-236">Para novo desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="8186c-236">For new development</span></span>        |[<span data-ttu-id="8186c-237">Razor Pages com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8186c-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="8186c-238">Para manter um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="8186c-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="8186c-239">MVC com o Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8186c-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="8186c-240">Leia uma visão geral de ASP.NET Core [conceitos básicos](xref:fundamentals/index) que se aplicam a todos os tipos de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="8186c-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="8186c-241">Pesquise no Sumário outros tópicos de interesse.</span><span class="sxs-lookup"><span data-stu-id="8186c-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="8186c-242">&dagger;Há também um [tutorial da API Web que você segue inteiramente no navegador](/learn/modules/build-web-api-net-core), nenhuma instalação local do IDE é necessária.</span><span class="sxs-lookup"><span data-stu-id="8186c-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="8186c-243">O código é executado em um [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) e [curl](https://curl.haxx.se/) é usado para teste.</span><span class="sxs-lookup"><span data-stu-id="8186c-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="8186c-244">Migrar do .NET Framework</span><span class="sxs-lookup"><span data-stu-id="8186c-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="8186c-245">Para obter um guia de referência para migrar aplicativos ASP.NET para <xref:migration/proper-to-2x/index>ASP.NET Core, consulte.</span><span class="sxs-lookup"><span data-stu-id="8186c-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="8186c-246">Como baixar uma amostra</span><span class="sxs-lookup"><span data-stu-id="8186c-246">How to download a sample</span></span>

<span data-ttu-id="8186c-247">Muitos dos artigos e tutoriais incluem links para exemplos de código.</span><span class="sxs-lookup"><span data-stu-id="8186c-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="8186c-248">[Baixe o arquivo zip do repositório ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="8186c-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="8186c-249">Descompacte o arquivo *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="8186c-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="8186c-250">Use a URL no link de exemplo para ajudá-lo a navegar até o diretório de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8186c-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="8186c-251">Diretivas do pré-processador no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="8186c-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="8186c-252">Para demonstrar vários cenários, aplicativos de exemplo usam `#define` as `#if-#else/#elif-#endif` diretivas de pré-processador e para compilar seletivamente e executar diferentes seções de código de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8186c-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="8186c-253">Para os exemplos que fazem uso dessa abordagem, defina a `#define` diretiva na parte superior dos arquivos C# para definir o símbolo associado ao cenário que você deseja executar.</span><span class="sxs-lookup"><span data-stu-id="8186c-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="8186c-254">Alguns exemplos exigem a definição do símbolo na parte superior de vários arquivos para executar um cenário.</span><span class="sxs-lookup"><span data-stu-id="8186c-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="8186c-255">Por exemplo, a seguinte lista de símbolo `#define` indica que quatro cenários estão disponíveis (um cenário por símbolo).</span><span class="sxs-lookup"><span data-stu-id="8186c-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="8186c-256">A configuração da amostra atual executa o cenário `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="8186c-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="8186c-257">Para alterar a amostra que executará o cenário `ExpandDefault`, defina o símbolo `ExpandDefault` e deixe os símbolos restantes comentados de fora:</span><span class="sxs-lookup"><span data-stu-id="8186c-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="8186c-258">Para obter mais informações sobre como usar [diretivas de pré-processador C#](/dotnet/csharp/language-reference/preprocessor-directives/) para compilar seletivamente as seções de código, consulte [#define (Referência C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) e [#if (Referência C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="8186c-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="8186c-259">Regiões no código de exemplo</span><span class="sxs-lookup"><span data-stu-id="8186c-259">Regions in sample code</span></span>

<span data-ttu-id="8186c-260">Alguns aplicativos de exemplo contêm seções de código circundadas por [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) e [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) diretivas C#.</span><span class="sxs-lookup"><span data-stu-id="8186c-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="8186c-261">O sistema de build de documentação injeta essas regiões nos tópicos renderizados da documentação.</span><span class="sxs-lookup"><span data-stu-id="8186c-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="8186c-262">Os nomes das regiões geralmente contêm a palavra "snippet".</span><span class="sxs-lookup"><span data-stu-id="8186c-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="8186c-263">O exemplo a seguir mostra uma região chamada `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="8186c-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="8186c-264">O snippet de código C# precedente é referenciado no arquivo de markdown do tópico com a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="8186c-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="8186c-265">Você pode ignorar (ou remover) com segurança as `#region` diretivas `#endregion` e que envolvem o código.</span><span class="sxs-lookup"><span data-stu-id="8186c-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="8186c-266">Não altere o código nessas diretivas se você planeja executar os cenários de exemplo descritos no tópico.</span><span class="sxs-lookup"><span data-stu-id="8186c-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="8186c-267">Fique à vontade para alterar o código ao experimentar com outros cenários.</span><span class="sxs-lookup"><span data-stu-id="8186c-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="8186c-268">Para obter mais informações, veja [Contribuir para a documentação do ASP.NET: snippets de código](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="8186c-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="8186c-269">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="8186c-269">Next steps</span></span>

<span data-ttu-id="8186c-270">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="8186c-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="8186c-271">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8186c-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="8186c-272">O [Community Standup semanal do ASP.NET](https://live.asp.net/) aborda o progresso e os planos da equipe.</span><span class="sxs-lookup"><span data-stu-id="8186c-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="8186c-273">Ele apresenta o novo software de terceiros e blogs.</span><span class="sxs-lookup"><span data-stu-id="8186c-273">It features new blogs and third-party software.</span></span>
