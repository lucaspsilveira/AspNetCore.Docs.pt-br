---
title: Integre Razor componentes de ASP.NET Core em Razor páginas e aplicativos MVC
author: guardrex
description: Saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 29360174ea86623491d5d8aacd4038162855bba8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399056"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="fddc2-103">Integre Razor componentes de ASP.NET Core em Razor páginas e aplicativos MVC</span><span class="sxs-lookup"><span data-stu-id="fddc2-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="fddc2-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="fddc2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="fddc2-105">os componentes podem ser integrados em Razor páginas e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="fddc2-105"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="fddc2-106">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="fddc2-107">Depois de [preparar o aplicativo](#prepare-the-app), use as diretrizes nas seções a seguir, dependendo dos requisitos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="fddc2-108">Componentes roteáveis: para componentes que são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="fddc2-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="fddc2-109">Siga estas diretrizes para que os visitantes possam fazer uma solicitação HTTP em seu navegador para um componente com uma [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="fddc2-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="fddc2-110">[Usar componentes roteáveis em um Razor aplicativo de páginas](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="fddc2-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="fddc2-111">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="fddc2-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="fddc2-112">[Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view): para componentes que não são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="fddc2-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="fddc2-113">Siga estas diretrizes quando o aplicativo inserir componentes em páginas e exibições existentes com o [auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fddc2-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="fddc2-114">Preparar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="fddc2-114">Prepare the app</span></span>

<span data-ttu-id="fddc2-115">Uma Razor página existente ou um aplicativo MVC pode integrar Razor componentes em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="fddc2-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="fddc2-116">No arquivo de layout do aplicativo ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="fddc2-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="fddc2-117">Adicione a seguinte `<base>` marcação ao `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="fddc2-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="fddc2-118">O `href` valor (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="fddc2-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="fddc2-119">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do <xref:blazor/host-and-deploy/index#app-base-path> artigo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="fddc2-120">O `_Layout.cshtml` arquivo está localizado na pasta *páginas/compartilhada* em um Razor aplicativo de páginas ou em *exibições/pastas compartilhadas* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="fddc2-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="fddc2-121">Adicione uma `<script>` marca para o script de *blazor.server.js* imediatamente antes da marca de fechamento `</body>` :</span><span class="sxs-lookup"><span data-stu-id="fddc2-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="fddc2-122">A estrutura adiciona o script *blazor.server.js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="fddc2-123">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="fddc2-124">Adicione um `_Imports.razor` arquivo à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace` , para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="fddc2-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="fddc2-125">No `Startup.ConfigureServices` , registre o Blazor Server serviço:</span><span class="sxs-lookup"><span data-stu-id="fddc2-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="fddc2-126">No `Startup.Configure` , adicione o Blazor ponto de extremidade do hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="fddc2-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="fddc2-127">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="fddc2-127">Integrate components into any page or view.</span></span> <span data-ttu-id="fddc2-128">Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="fddc2-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="fddc2-129">Usar componentes roteáveis em um Razor aplicativo de páginas</span><span class="sxs-lookup"><span data-stu-id="fddc2-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="fddc2-130">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="fddc2-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="fddc2-131">Para dar suporte a componentes roteáveis Razor em Razor páginas aplicativos:</span><span class="sxs-lookup"><span data-stu-id="fddc2-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="fddc2-132">Siga as orientações na seção [preparar o aplicativo](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="fddc2-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="fddc2-133">Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-133">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="fddc2-134">Adicione um `_Host.cshtml` arquivo à `Pages` pasta com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="fddc2-135">Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="fddc2-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="fddc2-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="fddc2-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="fddc2-137">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="fddc2-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="fddc2-138">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="fddc2-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="fddc2-139">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="fddc2-139">Render Mode</span></span> | <span data-ttu-id="fddc2-140">Descrição</span><span class="sxs-lookup"><span data-stu-id="fddc2-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="fddc2-141">Renderiza o `App` componente em HTML estático e inclui um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="fddc2-142">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="fddc2-143">Renderiza um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="fddc2-144">A saída do `App` componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="fddc2-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="fddc2-145">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="fddc2-146">Renderiza o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="fddc2-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="fddc2-147">Para obter mais informações sobre o auxiliar de marca de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="fddc2-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="fddc2-148">Adicione uma rota de baixa prioridade à `_Host.cshtml` página para a configuração de ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="fddc2-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="fddc2-149">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-149">Add routable components to the app.</span></span> <span data-ttu-id="fddc2-150">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="fddc2-151">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="fddc2-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="fddc2-152">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="fddc2-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="fddc2-153">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="fddc2-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="fddc2-154">Para dar suporte a componentes roteáveis Razor em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="fddc2-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="fddc2-155">Siga as orientações na seção [preparar o aplicativo](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="fddc2-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="fddc2-156">Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="fddc2-157">Adicione um `_Host.cshtml` arquivo à `Views/Home` pasta com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="fddc2-158">Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="fddc2-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="fddc2-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="fddc2-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="fddc2-160">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="fddc2-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="fddc2-161">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="fddc2-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="fddc2-162">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="fddc2-162">Render Mode</span></span> | <span data-ttu-id="fddc2-163">Descrição</span><span class="sxs-lookup"><span data-stu-id="fddc2-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="fddc2-164">Renderiza o `App` componente em HTML estático e inclui um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="fddc2-165">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="fddc2-166">Renderiza um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="fddc2-167">A saída do `App` componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="fddc2-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="fddc2-168">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="fddc2-169">Renderiza o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="fddc2-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="fddc2-170">Para obter mais informações sobre o auxiliar de marca de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="fddc2-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="fddc2-171">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="fddc2-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="fddc2-172">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a `_Host.cshtml` exibição para a configuração do ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="fddc2-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="fddc2-173">Crie uma `Pages` pasta e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="fddc2-174">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="fddc2-175">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="fddc2-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="fddc2-176">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="fddc2-176">Render components from a page or view</span></span>

<span data-ttu-id="fddc2-177">*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="fddc2-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="fddc2-178">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="fddc2-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="fddc2-179">Renderizar componentes interativos com estado</span><span class="sxs-lookup"><span data-stu-id="fddc2-179">Render stateful interactive components</span></span>

<span data-ttu-id="fddc2-180">Componentes interativos com estado podem ser adicionados a uma Razor página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="fddc2-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="fddc2-181">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="fddc2-181">When the page or view renders:</span></span>

* <span data-ttu-id="fddc2-182">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="fddc2-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="fddc2-183">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="fddc2-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="fddc2-184">O novo estado do componente é criado quando a SignalR conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="fddc2-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="fddc2-185">A página a seguir Razor renderiza um `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="fddc2-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="fddc2-186">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="fddc2-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="fddc2-187">Renderizar componentes não interativos</span><span class="sxs-lookup"><span data-stu-id="fddc2-187">Render noninteractive components</span></span>

<span data-ttu-id="fddc2-188">Na página a seguir Razor , o `Counter` componente é processado estaticamente com um valor inicial que é especificado usando um formulário.</span><span class="sxs-lookup"><span data-stu-id="fddc2-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="fddc2-189">Como o componente é processado estaticamente, o componente não é interativo:</span><span class="sxs-lookup"><span data-stu-id="fddc2-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="fddc2-190">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="fddc2-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="fddc2-191">Namespaces de componente</span><span class="sxs-lookup"><span data-stu-id="fddc2-191">Component namespaces</span></span>

<span data-ttu-id="fddc2-192">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao `_ViewImports.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="fddc2-193">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="fddc2-193">In the following example:</span></span>

* <span data-ttu-id="fddc2-194">Altere `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddc2-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="fddc2-195">Se uma pasta chamada *Components* não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.</span><span class="sxs-lookup"><span data-stu-id="fddc2-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="fddc2-196">O `_ViewImports.cshtml` arquivo está localizado na `Pages` pasta de um Razor aplicativo de páginas ou na `Views` pasta de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="fddc2-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="fddc2-197">Para obter mais informações, consulte <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="fddc2-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
