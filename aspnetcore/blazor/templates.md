---
title: Modelos Blazor de ASP.NET Core
author: guardrex
description: Saiba mais sobre Blazor os modelos de Blazor aplicativo ASP.NET Core e a estrutura do projeto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: c84d6415728bf56836d98cfa66d1b9d46d2eadc8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770895"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="9e45f-103">Modelos Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e45f-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="9e45f-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9e45f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="9e45f-105">A Blazor estrutura fornece modelos para desenvolver aplicativos para cada um dos Blazor modelos de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="9e45f-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="9e45f-106">Webassembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="9e45f-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="9e45f-107">Servidor (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="9e45f-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="9e45f-108">Para obter mais informações Blazorsobre os modelos de hospedagem <xref:blazor/hosting-models>do, consulte.</span><span class="sxs-lookup"><span data-stu-id="9e45f-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="9e45f-109">Para obter as instruções passo a passo sobre como criar Blazor um aplicativo a partir de um <xref:blazor/get-started>modelo, consulte.</span><span class="sxs-lookup"><span data-stu-id="9e45f-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="9e45f-110">As opções de modelo estão disponíveis passando `--help` a opção para o [novo comando dotnet](/dotnet/core/tools/dotnet-new) da CLI:</span><span class="sxs-lookup"><span data-stu-id="9e45f-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="9e45f-111">estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="9e45f-111"> project structure</span></span>

<span data-ttu-id="9e45f-112">Os seguintes arquivos e pastas compõem um Blazor aplicativo gerado a partir Blazor de um modelo:</span><span class="sxs-lookup"><span data-stu-id="9e45f-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="9e45f-113">*Program.cs* &ndash; o ponto de entrada do aplicativo que configura:</span><span class="sxs-lookup"><span data-stu-id="9e45f-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="9e45f-114">[Host](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor servidor)</span><span class="sxs-lookup"><span data-stu-id="9e45f-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="9e45f-115">Host Webassembly (Blazor Webassembly) &ndash; o código nesse arquivo é exclusivo para aplicativos criados a Blazor partir do modelo Webassembly (`blazorwasm`).</span><span class="sxs-lookup"><span data-stu-id="9e45f-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="9e45f-116">O `App` componente, que é o componente raiz do aplicativo, é especificado como o `app` elemento DOM para o `Add` método.</span><span class="sxs-lookup"><span data-stu-id="9e45f-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="9e45f-117">Os serviços podem ser configurados com o `ConfigureServices` método no construtor de hosts `builder.Services.AddSingleton<IMyDependency, MyDependency>();`(por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="9e45f-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="9e45f-118">A configuração pode ser fornecida por meio do host`builder.Configuration`Builder ().</span><span class="sxs-lookup"><span data-stu-id="9e45f-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="9e45f-119">*Startup.cs* (Blazor servidor) &ndash; contém a lógica de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="9e45f-120">A `Startup` classe define dois métodos:</span><span class="sxs-lookup"><span data-stu-id="9e45f-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="9e45f-121">`ConfigureServices`&ndash; Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="9e45f-122">Em Blazor aplicativos de servidor, os serviços são adicionados <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>chamando e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente `FetchData` de exemplo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="9e45f-123">`Configure`&ndash; Configura o pipeline de tratamento de solicitação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9e45f-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="9e45f-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador.</span><span class="sxs-lookup"><span data-stu-id="9e45f-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="9e45f-125">A conexão é criada com [SignalR](xref:signalr/introduction), que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.</span><span class="sxs-lookup"><span data-stu-id="9e45f-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="9e45f-126">[MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo (*pages/_Host. cshtml*) e habilitar a navegação.</span><span class="sxs-lookup"><span data-stu-id="9e45f-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="9e45f-127">*wwwroot/index.html* (Blazor Webassembly) &ndash; a página raiz do aplicativo implementada como uma página HTML:</span><span class="sxs-lookup"><span data-stu-id="9e45f-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="9e45f-128">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="9e45f-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="9e45f-129">A página especifica onde o componente `App` raiz é renderizado.</span><span class="sxs-lookup"><span data-stu-id="9e45f-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="9e45f-130">O `App` componente (*app. Razor*) é especificado como o `app` elemento DOM para o `AddComponent` método em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9e45f-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="9e45f-131">O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:</span><span class="sxs-lookup"><span data-stu-id="9e45f-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="9e45f-132">Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="9e45f-133">Inicializa o tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="9e45f-134">*App. Razor* &ndash; o componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente.</span><span class="sxs-lookup"><span data-stu-id="9e45f-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="9e45f-135">O `Router` componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.</span><span class="sxs-lookup"><span data-stu-id="9e45f-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="9e45f-136">*Pages* A pasta &ndash; páginas contém os componentes/páginas roteáveis (*. Razor*) que compõem o Blazor aplicativo e Razor a página raiz Blazor de um aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="9e45f-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="9e45f-137">A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="9e45f-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="9e45f-138">O modelo inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="9e45f-138">The template includes the following:</span></span>
  * <span data-ttu-id="9e45f-139">*_Host. cshtml* (Blazor servidor) &ndash; a página raiz do aplicativo implementada como uma Razor página:</span><span class="sxs-lookup"><span data-stu-id="9e45f-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="9e45f-140">Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.</span><span class="sxs-lookup"><span data-stu-id="9e45f-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="9e45f-141">O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que configura a conexão em tempo SignalR real entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="9e45f-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="9e45f-142">A página host especifica onde o componente `App` raiz (*app. Razor*) é renderizado.</span><span class="sxs-lookup"><span data-stu-id="9e45f-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="9e45f-143">`Counter`(*Counter. Razor*) &ndash; implementa a página do contador.</span><span class="sxs-lookup"><span data-stu-id="9e45f-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="9e45f-144">`Error`(*Error. Razor*, Blazor somente aplicativo de servidor) &ndash; Renderizado quando ocorre uma exceção sem tratamento no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="9e45f-145">`FetchData`(*FetchData. Razor*) &ndash; implementa a página buscar dados.</span><span class="sxs-lookup"><span data-stu-id="9e45f-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="9e45f-146">`Index`(*Index. Razor*) &ndash; implementa a Home Page.</span><span class="sxs-lookup"><span data-stu-id="9e45f-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="9e45f-147">*Shared* A pasta &ndash; compartilhada contém outros componentes de interface do usuário (*. Razor*) usados pelo aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9e45f-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="9e45f-148">`MainLayout`(*MainLayout. Razor*) &ndash; o componente de layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="9e45f-149">`NavMenu`(*NavMenu. Razor*) &ndash; implementa a navegação na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="9e45f-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="9e45f-150">Inclui o [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que renderiza links de navegação para outros Razor componentes.</span><span class="sxs-lookup"><span data-stu-id="9e45f-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="9e45f-151">O `NavLink` componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.</span><span class="sxs-lookup"><span data-stu-id="9e45f-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="9e45f-152">*_Imports. Razor* &ndash; inclui diretivas Razor comuns para incluir nos componentes do aplicativo (*. Razor*), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.</span><span class="sxs-lookup"><span data-stu-id="9e45f-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="9e45f-153">*Data* A pasta deBlazor dados ( &ndash; servidor) `WeatherForecast` contém a classe e a `WeatherForecastService` implementação do que fornecem dados meteorológicos de exemplo `FetchData` para o componente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="9e45f-154">*wwwroot* &ndash; a pasta [raiz da Web](xref:fundamentals/index#web-root) do aplicativo que contém os ativos estáticos públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="9e45f-155">parâmetros de configuração *appSettings. JSON* (Blazor Server) &ndash; para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9e45f-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
