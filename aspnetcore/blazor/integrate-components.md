---
title: Integre ASP.NET componentes do Core Razor em páginas de barbear e aplicativos MVC
author: guardrex
description: Saiba mais sobre cenários de vinculação Blazor de dados para componentes e elementos DOM em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: c242fbef70d289929d5c005abc0aa431619862b3
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383962"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="5308a-103">Integre ASP.NET componentes do Core Razor em páginas de barbear e aplicativos MVC</span><span class="sxs-lookup"><span data-stu-id="5308a-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="5308a-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5308a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5308a-105">Os componentes da navalha podem ser integrados em páginas de barbear e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="5308a-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="5308a-106">Quando a página ou exibição é renderizada, os componentes podem ser pré-renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="5308a-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="5308a-107">Depois [de preparar o aplicativo,](#prepare-the-app)use a orientação nas seguintes seções, dependendo dos requisitos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5308a-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="5308a-108">Componentes &ndash; routable Para componentes que são diretamente roteadores de solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="5308a-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="5308a-109">Siga esta orientação quando os visitantes devem ser capazes [`@page`](xref:mvc/views/razor#page) de fazer uma solicitação HTTP em seu navegador para um componente com uma diretiva.</span><span class="sxs-lookup"><span data-stu-id="5308a-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="5308a-110">Use componentes roteáveis em um aplicativo Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5308a-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="5308a-111">Use componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="5308a-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="5308a-112">[Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) &ndash; Para componentes que não são diretamente roteirizadores a partir de solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="5308a-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="5308a-113">Siga esta orientação quando o aplicativo incorporar componentes em páginas e exibições existentes com o [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5308a-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="5308a-114">Preparar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="5308a-114">Prepare the app</span></span>

<span data-ttu-id="5308a-115">Um aplicativo De navalha ou MVC existente pode integrar componentes do Razor em páginas e visualizações:</span><span class="sxs-lookup"><span data-stu-id="5308a-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="5308a-116">No arquivo de layout do aplicativo *(_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="5308a-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="5308a-117">Adicione a `<base>` seguinte `<head>` tag ao elemento:</span><span class="sxs-lookup"><span data-stu-id="5308a-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="5308a-118">O `href` valor (o caminho base do *aplicativo)* no exemplo anterior assume que`/`o aplicativo reside no caminho raiz da URL ( ).</span><span class="sxs-lookup"><span data-stu-id="5308a-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="5308a-119">Se o aplicativo for um subaplicativo, siga a orientação <xref:host-and-deploy/blazor/index#app-base-path> na seção de caminho base do *aplicativo* do artigo.</span><span class="sxs-lookup"><span data-stu-id="5308a-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="5308a-120">O arquivo *_Layout.cshtml* está localizado na pasta *Páginas/Compartilhados* em um aplicativo Razor Pages ou na pasta *Views/Shared* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="5308a-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="5308a-121">Adicione `<script>` uma tag para o script *blazor.server.js* imediatamente antes da tag de fechamento: `</body>`</span><span class="sxs-lookup"><span data-stu-id="5308a-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="5308a-122">A estrutura adiciona o script *blazor.server.js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="5308a-123">Não há necessidade de adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="5308a-124">Adicione um arquivo *_Imports.razor* à pasta raiz do projeto com o `MyAppNamespace`seguinte conteúdo (altere o último namespace, para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="5308a-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="5308a-125">Em `Startup.ConfigureServices`, registrar o serviço Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="5308a-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="5308a-126">Em `Startup.Configure`, adicionar o ponto `app.UseEndpoints`final do Blazor Hub a:</span><span class="sxs-lookup"><span data-stu-id="5308a-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="5308a-127">Integre os componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="5308a-127">Integrate components into any page or view.</span></span> <span data-ttu-id="5308a-128">Para obter mais informações, consulte os [componentes Render de uma página ou seção de exibição.](#render-components-from-a-page-or-view)</span><span class="sxs-lookup"><span data-stu-id="5308a-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="5308a-129">Use componentes roteáveis em um aplicativo Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5308a-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="5308a-130">*Esta seção diz respeito à adição de componentes que são diretamente roteiráveis a partir de solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="5308a-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5308a-131">Para suportar componentes de navalha routable em aplicativos Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="5308a-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="5308a-132">Siga as orientações na seção [Prepare o aplicativo.](#prepare-the-app)</span><span class="sxs-lookup"><span data-stu-id="5308a-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5308a-133">Adicione um arquivo *App.razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5308a-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="5308a-134">Adicione um arquivo *_Host.cshtml* à pasta *Páginas* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5308a-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5308a-135">Os componentes usam o arquivo *_Layout.cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="5308a-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="5308a-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se `App` o componente:</span><span class="sxs-lookup"><span data-stu-id="5308a-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5308a-137">É pré-renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="5308a-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5308a-138">É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.</span><span class="sxs-lookup"><span data-stu-id="5308a-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5308a-139">Modo renderização</span><span class="sxs-lookup"><span data-stu-id="5308a-139">Render Mode</span></span> | <span data-ttu-id="5308a-140">Descrição</span><span class="sxs-lookup"><span data-stu-id="5308a-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5308a-141">Renderiza o `App` componente em HTML estático e inclui um marcador para um aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="5308a-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5308a-142">Quando o usuário-agente é iniciado, este marcador é usado para bootstrap de um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="5308a-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5308a-143">Torna um marcador para um aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="5308a-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5308a-144">A saída `App` do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="5308a-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5308a-145">Quando o usuário-agente é iniciado, este marcador é usado para bootstrap de um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="5308a-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5308a-146">Torna o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="5308a-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5308a-147">Para obter mais informações sobre o <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>Component Tag Helper, consulte .</span><span class="sxs-lookup"><span data-stu-id="5308a-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5308a-148">Adicione uma rota de baixa prioridade para a página *_Host.cshtml* para a configuração de ponto final em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5308a-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="5308a-149">Adicione componentes rotáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-149">Add routable components to the app.</span></span> <span data-ttu-id="5308a-150">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5308a-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5308a-151">Para obter mais informações sobre namespaces, consulte a seção [Espaços de nomes componentes.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="5308a-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="5308a-152">Use componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="5308a-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="5308a-153">*Esta seção diz respeito à adição de componentes que são diretamente roteiráveis a partir de solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="5308a-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5308a-154">Para suportar componentes de navalha routable em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="5308a-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="5308a-155">Siga as orientações na seção [Prepare o aplicativo.](#prepare-the-app)</span><span class="sxs-lookup"><span data-stu-id="5308a-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5308a-156">Adicione um arquivo *App.razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5308a-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="5308a-157">Adicione um arquivo *_Host.cshtml* à pasta *Views/Home* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5308a-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5308a-158">Os componentes usam o arquivo *_Layout.cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="5308a-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="5308a-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se `App` o componente:</span><span class="sxs-lookup"><span data-stu-id="5308a-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5308a-160">É pré-renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="5308a-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5308a-161">É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.</span><span class="sxs-lookup"><span data-stu-id="5308a-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5308a-162">Modo renderização</span><span class="sxs-lookup"><span data-stu-id="5308a-162">Render Mode</span></span> | <span data-ttu-id="5308a-163">Descrição</span><span class="sxs-lookup"><span data-stu-id="5308a-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5308a-164">Renderiza o `App` componente em HTML estático e Blazor inclui um marcador para um aplicativo Server.</span><span class="sxs-lookup"><span data-stu-id="5308a-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5308a-165">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5308a-166">Renderiza um marcador Blazor para um aplicativo do Servidor.</span><span class="sxs-lookup"><span data-stu-id="5308a-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5308a-167">A saída `App` do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="5308a-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5308a-168">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5308a-169">Torna o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="5308a-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5308a-170">Para obter mais informações sobre o <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>Component Tag Helper, consulte .</span><span class="sxs-lookup"><span data-stu-id="5308a-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5308a-171">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="5308a-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="5308a-172">Adicione uma rota de baixa prioridade para a ação do controlador que retorna `Startup.Configure`a exibição *_Host.cshtml* à configuração do ponto final em :</span><span class="sxs-lookup"><span data-stu-id="5308a-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="5308a-173">Crie uma pasta *Páginas* e adicione componentes rotáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="5308a-174">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5308a-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5308a-175">Para obter mais informações sobre namespaces, consulte a seção [Espaços de nomes componentes.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="5308a-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="5308a-176">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="5308a-176">Render components from a page or view</span></span>

<span data-ttu-id="5308a-177">*Esta seção diz respeito à adição de componentes a páginas ou visualizações, onde os componentes não são diretamente roteiristas a partir de solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="5308a-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="5308a-178">Para renderizar um componente de uma página ou exibição, use o [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5308a-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="5308a-179">Para obter mais informações sobre como os componentes `Component` são renderizados, o estado do componente e o Tag Helper, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="5308a-179">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>

## <a name="component-namespaces"></a><span data-ttu-id="5308a-180">Espaços de nomes de componentes</span><span class="sxs-lookup"><span data-stu-id="5308a-180">Component namespaces</span></span>

<span data-ttu-id="5308a-181">Ao usar uma pasta personalizada para segurar os componentes do aplicativo, adicione o namespace representando a pasta à página/exibição ou ao arquivo *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="5308a-181">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5308a-182">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5308a-182">In the following example:</span></span>

* <span data-ttu-id="5308a-183">Mude `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5308a-183">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="5308a-184">Se uma pasta chamada *Componentes* não for usada `Components` para segurar os componentes, mude para a pasta onde os componentes residem.</span><span class="sxs-lookup"><span data-stu-id="5308a-184">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="5308a-185">O arquivo *_ViewImports.cshtml* está localizado na pasta *Páginas* de um aplicativo Razor Pages ou na pasta *Views* de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="5308a-185">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="5308a-186">Para obter mais informações, consulte <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="5308a-186">For more information, see <xref:blazor/components#import-components>.</span></span>
