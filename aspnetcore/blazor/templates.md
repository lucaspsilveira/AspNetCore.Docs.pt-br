---
title: ASP.NET Blazor modelos Core
author: guardrex
description: Conheça ASP.NET Blazor modelos de Blazor aplicativos Core e estrutura do projeto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661722"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="a3004-103">ASP.NET Blazor modelos Core</span><span class="sxs-lookup"><span data-stu-id="a3004-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="a3004-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a3004-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a3004-105">A Blazor estrutura fornece modelos para desenvolver Blazor aplicativos para cada um dos modelos de hospedagem:</span><span class="sxs-lookup"><span data-stu-id="a3004-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="a3004-106">WebAssembly`blazorwasm`( )</span><span class="sxs-lookup"><span data-stu-id="a3004-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="a3004-107">Servidor`blazorserver`( )</span><span class="sxs-lookup"><span data-stu-id="a3004-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="a3004-108">Para obter Blazormais informações sobre <xref:blazor/hosting-models>os modelos de hospedagem, consulte .</span><span class="sxs-lookup"><span data-stu-id="a3004-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="a3004-109">Para obter instruções passo a Blazor passo sobre a <xref:blazor/get-started>criação de um aplicativo a partir de um modelo, consulte .</span><span class="sxs-lookup"><span data-stu-id="a3004-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="a3004-110">As opções de `--help` modelo estão disponíveis passando a opção para o novo comando CLI [dotnet:](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="a3004-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="a3004-111">estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="a3004-111"> project structure</span></span>

<span data-ttu-id="a3004-112">Os seguintes arquivos e Blazor pastas compõem um aplicativo gerado a partir de um Blazor modelo:</span><span class="sxs-lookup"><span data-stu-id="a3004-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="a3004-113">*Program.cs* &ndash; O ponto de entrada do aplicativo que configura:</span><span class="sxs-lookup"><span data-stu-id="a3004-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="a3004-114">host [host](xref:fundamentals/host/generic-host) ASP.NETBlazor Core (Servidor)</span><span class="sxs-lookup"><span data-stu-id="a3004-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="a3004-115">Host WebAssemblyBlazor (WebAssembly) &ndash; O código neste arquivo é Blazor exclusivo para`blazorwasm`aplicativos criados a partir do modelo WebAssembly ( ).</span><span class="sxs-lookup"><span data-stu-id="a3004-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="a3004-116">O `App` componente, que é o componente raiz do `app` aplicativo, é `Add` especificado como o elemento DOM para o método.</span><span class="sxs-lookup"><span data-stu-id="a3004-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="a3004-117">Os serviços podem `ConfigureServices` ser configurados com o `builder.Services.AddSingleton<IMyDependency, MyDependency>();`método no construtor de host (por exemplo, ).</span><span class="sxs-lookup"><span data-stu-id="a3004-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="a3004-118">A configuração pode ser`builder.Configuration`fornecida através do construtor host ( ).</span><span class="sxs-lookup"><span data-stu-id="a3004-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="a3004-119">*Startup.cs* Blazor (Servidor) &ndash; Contém a lógica de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="a3004-120">A `Startup` classe define dois métodos:</span><span class="sxs-lookup"><span data-stu-id="a3004-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="a3004-121">`ConfigureServices`&ndash; Configura os serviços de [injeção de dependência (DI)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="a3004-122">Nos Blazor aplicativos server, os <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>serviços `WeatherForecastService` são adicionados por chamada , `FetchData` e o é adicionado ao contêiner de serviço para uso pelo componente exemplo.</span><span class="sxs-lookup"><span data-stu-id="a3004-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="a3004-123">`Configure`&ndash; Configura o pipeline de tratamento de solicitação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a3004-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="a3004-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>é chamado para configurar um ponto final para a conexão em tempo real com o navegador.</span><span class="sxs-lookup"><span data-stu-id="a3004-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="a3004-125">A conexão é [SignalR](xref:signalr/introduction)criada com , que é uma estrutura para adicionar funcionalidade web em tempo real aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a3004-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="a3004-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo *(Pages/_Host.cshtml)* e ativar a navegação.</span><span class="sxs-lookup"><span data-stu-id="a3004-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="a3004-127">*wwwroot/index.html* Blazor ( WebAssembly) &ndash; A página raiz do aplicativo implementada como uma página HTML:</span><span class="sxs-lookup"><span data-stu-id="a3004-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a3004-128">Quando qualquer página do aplicativo é solicitada inicialmente, esta página é renderizada e devolvida na resposta.</span><span class="sxs-lookup"><span data-stu-id="a3004-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a3004-129">A página especifica onde `App` o componente raiz é renderizado.</span><span class="sxs-lookup"><span data-stu-id="a3004-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a3004-130">O `App` componente *(App.razor)* é `app` especificado como `AddComponent` o `Startup.Configure`elemento DOM para o método em .</span><span class="sxs-lookup"><span data-stu-id="a3004-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="a3004-131">O `_framework/blazor.webassembly.js` arquivo JavaScript está carregado, o que:</span><span class="sxs-lookup"><span data-stu-id="a3004-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="a3004-132">Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="a3004-133">Inicializa o tempo de execução para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="a3004-134">*App.razor* &ndash; O componente raiz do aplicativo que configura roteamento ao lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente.</span><span class="sxs-lookup"><span data-stu-id="a3004-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a3004-135">O `Router` componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.</span><span class="sxs-lookup"><span data-stu-id="a3004-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="a3004-136">*Pasta* &ndash; páginas Contém os componentes/páginas routable *(.razor)* que compõem Blazor Blazor o aplicativo e a página navalha raiz de um aplicativo server.</span><span class="sxs-lookup"><span data-stu-id="a3004-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="a3004-137">A rota para cada página [`@page`](xref:mvc/views/razor#page) é especificada usando a diretiva.</span><span class="sxs-lookup"><span data-stu-id="a3004-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a3004-138">O modelo inclui o seguinte:</span><span class="sxs-lookup"><span data-stu-id="a3004-138">The template includes the following:</span></span>
  * <span data-ttu-id="a3004-139">*_Host.cshtml* Blazor (Servidor) &ndash; A página raiz do aplicativo implementada como uma página de navalha:</span><span class="sxs-lookup"><span data-stu-id="a3004-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="a3004-140">Quando qualquer página do aplicativo é solicitada inicialmente, esta página é renderizada e devolvida na resposta.</span><span class="sxs-lookup"><span data-stu-id="a3004-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="a3004-141">O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que SignalR configura a conexão em tempo real entre o navegador e o servidor.</span><span class="sxs-lookup"><span data-stu-id="a3004-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="a3004-142">A página Host especifica `App` onde o componente raiz *(App.razor)* é renderizado.</span><span class="sxs-lookup"><span data-stu-id="a3004-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="a3004-143">`Counter`*(Counter.razor* &ndash; ) Implementa a página Contador.</span><span class="sxs-lookup"><span data-stu-id="a3004-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="a3004-144">`Error`(*Erro.razor*, Blazor somente aplicativo do servidor) &ndash; Renderizado quando uma exceção não tratada ocorre no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="a3004-145">`FetchData`*(FetchData.razor)* &ndash; Implementa a página de dados do Fetch.</span><span class="sxs-lookup"><span data-stu-id="a3004-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a3004-146">`Index`*(Index.razor)* &ndash; Implementa a home page.</span><span class="sxs-lookup"><span data-stu-id="a3004-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="a3004-147">*Pasta* compartilhada &ndash; Contém outros componentes de IU *(.razor)* usados pelo aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a3004-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="a3004-148">`MainLayout`(*MainLayout.razor*) &ndash; O componente de layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="a3004-149">`NavMenu`(*NavMenu.razor*) &ndash; Implementa navegação de barra lateral.</span><span class="sxs-lookup"><span data-stu-id="a3004-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="a3004-150">Inclui o [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que renderiza links de navegação para outros componentes da Navalha.</span><span class="sxs-lookup"><span data-stu-id="a3004-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a3004-151">O `NavLink` componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente é exibido atualmente.</span><span class="sxs-lookup"><span data-stu-id="a3004-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="a3004-152">*_Imports.razor* &ndash; Inclui diretivas comuns de Razor para incluir nos componentes [`@using`](xref:mvc/views/razor#using) do aplicativo *(.razor),* como diretivas para namespaces.</span><span class="sxs-lookup"><span data-stu-id="a3004-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a3004-153">*Pasta* deBlazor dados &ndash; (Servidor) Contém `WeatherForecast` `WeatherForecastService` a classe e a implementação `FetchData` dos que fornecem dados meteorológicos de exemplo para o componente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="a3004-154">*wwwroot* &ndash; A pasta [Web Root](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="a3004-155">*configurações de appsettings.json* (Servidor)Blazor &ndash; Configurações para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3004-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
