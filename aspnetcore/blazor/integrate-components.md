---
title: Integre ASP.NET componentes do Core Razor em páginas de barbear e aplicativos MVC
author: guardrex
description: Saiba mais sobre cenários de vinculação Blazor de dados para componentes e elementos DOM em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 6efa84c550a4605bde5e1f2bca4f2d1aa4a2667b
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123358"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="f3453-103">Integre ASP.NET componentes do Core Razor em páginas de barbear e aplicativos MVC</span><span class="sxs-lookup"><span data-stu-id="f3453-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="f3453-104">Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f3453-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f3453-105">Os componentes da navalha podem ser integrados em páginas de barbear e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="f3453-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="f3453-106">Quando a página ou exibição é renderizada, os componentes podem ser pré-renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="f3453-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="f3453-107">Prepare o aplicativo para usar componentes em páginas e visualizações</span><span class="sxs-lookup"><span data-stu-id="f3453-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="f3453-108">Um aplicativo De navalha ou MVC existente pode integrar componentes do Razor em páginas e visualizações:</span><span class="sxs-lookup"><span data-stu-id="f3453-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="f3453-109">No arquivo de layout do aplicativo *(_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f3453-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="f3453-110">Adicione a `<base>` seguinte `<head>` tag ao elemento:</span><span class="sxs-lookup"><span data-stu-id="f3453-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="f3453-111">O `href` valor (o caminho base do *aplicativo)* no exemplo anterior assume que`/`o aplicativo reside no caminho raiz da URL ( ).</span><span class="sxs-lookup"><span data-stu-id="f3453-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="f3453-112">Se o aplicativo for um subaplicativo, siga a orientação <xref:host-and-deploy/blazor/index#app-base-path> na seção de caminho base do *aplicativo* do artigo.</span><span class="sxs-lookup"><span data-stu-id="f3453-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="f3453-113">O arquivo *_Layout.cshtml* está localizado na pasta *Páginas/Compartilhados* em um aplicativo Razor Pages ou na pasta *Views/Shared* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="f3453-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="f3453-114">Adicione `<script>` uma tag para o script *blazor.server.js* imediatamente antes da tag de fechamento: `</body>`</span><span class="sxs-lookup"><span data-stu-id="f3453-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="f3453-115">A estrutura adiciona o script *blazor.server.js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="f3453-116">Não há necessidade de adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="f3453-117">Adicione um arquivo *_Imports.razor* à pasta raiz do projeto com o `MyAppNamespace`seguinte conteúdo (altere o último namespace, para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="f3453-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="f3453-118">Em `Startup.ConfigureServices`, registrar o serviço Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="f3453-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="f3453-119">Em `Startup.Configure`, adicionar o ponto `app.UseEndpoints`final do Blazor Hub a:</span><span class="sxs-lookup"><span data-stu-id="f3453-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="f3453-120">Integre os componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="f3453-120">Integrate components into any page or view.</span></span> <span data-ttu-id="f3453-121">Para obter mais informações, consulte os [componentes Render de uma página ou seção de exibição.](#render-components-from-a-page-or-view)</span><span class="sxs-lookup"><span data-stu-id="f3453-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="f3453-122">Use componentes roteáveis em um aplicativo Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f3453-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="f3453-123">*Esta seção diz respeito à adição de componentes que são diretamente roteiráveis a partir de solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="f3453-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f3453-124">Para suportar componentes de navalha routable em aplicativos Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="f3453-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="f3453-125">Siga a orientação no Prepare o aplicativo para usar componentes na seção [páginas e visualizações.](#prepare-the-app-to-use-components-in-pages-and-views)</span><span class="sxs-lookup"><span data-stu-id="f3453-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="f3453-126">Adicione um arquivo *App.razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="f3453-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="f3453-127">Adicione um arquivo *_Host.cshtml* à pasta *Páginas* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="f3453-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f3453-128">Os componentes usam o arquivo *_Layout.cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="f3453-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="f3453-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se `App` o componente:</span><span class="sxs-lookup"><span data-stu-id="f3453-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="f3453-130">É pré-renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="f3453-130">Is prerendered into the page.</span></span>
   * <span data-ttu-id="f3453-131">É renderizado como HTML estático na página ou se inclui as informações necessárias para bootstrap um aplicativo Blazor do agente usuário.</span><span class="sxs-lookup"><span data-stu-id="f3453-131">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="f3453-132">Modo renderização</span><span class="sxs-lookup"><span data-stu-id="f3453-132">Render Mode</span></span> | <span data-ttu-id="f3453-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="f3453-133">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f3453-134">Renderiza o `App` componente em HTML estático e Blazor inclui um marcador para um aplicativo Server.</span><span class="sxs-lookup"><span data-stu-id="f3453-134">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f3453-135">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-135">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f3453-136">Renderiza um marcador Blazor para um aplicativo do Servidor.</span><span class="sxs-lookup"><span data-stu-id="f3453-136">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f3453-137">A saída `App` do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="f3453-137">Output from the `App` component isn't included.</span></span> <span data-ttu-id="f3453-138">Quando o usuário-agente é iniciado, este Blazor marcador é usado para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-138">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f3453-139">Torna o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="f3453-139">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="f3453-140">Para obter mais informações sobre o <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>Component Tag Helper, consulte .</span><span class="sxs-lookup"><span data-stu-id="f3453-140">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="f3453-141">Adicione uma rota de baixa prioridade para a página *_Host.cshtml* para a configuração de ponto final em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f3453-141">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="f3453-142">Adicione componentes rotáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-142">Add routable components to the app.</span></span> <span data-ttu-id="f3453-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f3453-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="f3453-144">Para obter mais informações sobre namespaces, consulte a seção [Espaços de nomes componentes.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="f3453-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="f3453-145">Use componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="f3453-145">Use routable components in an MVC app</span></span>

<span data-ttu-id="f3453-146">*Esta seção diz respeito à adição de componentes que são diretamente roteiráveis a partir de solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="f3453-146">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="f3453-147">Para suportar componentes de navalha routable em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="f3453-147">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="f3453-148">Siga a orientação no Prepare o aplicativo para usar componentes na seção [páginas e visualizações.](#prepare-the-app-to-use-components-in-pages-and-views)</span><span class="sxs-lookup"><span data-stu-id="f3453-148">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="f3453-149">Adicione um arquivo *App.razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="f3453-149">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="f3453-150">Adicione um arquivo *_Host.cshtml* à pasta *Views/Home* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="f3453-150">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="f3453-151">Os componentes usam o arquivo *_Layout.cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="f3453-151">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="f3453-152">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="f3453-152">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="f3453-153">Adicione uma rota de baixa prioridade para a ação do controlador que retorna `Startup.Configure`a exibição *_Host.cshtml* à configuração do ponto final em :</span><span class="sxs-lookup"><span data-stu-id="f3453-153">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="f3453-154">Crie uma pasta *Páginas* e adicione componentes rotáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-154">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="f3453-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f3453-155">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="f3453-156">Para obter mais informações sobre namespaces, consulte a seção [Espaços de nomes componentes.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="f3453-156">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="f3453-157">Espaços de nomes de componentes</span><span class="sxs-lookup"><span data-stu-id="f3453-157">Component namespaces</span></span>

<span data-ttu-id="f3453-158">Ao usar uma pasta personalizada para segurar os componentes do aplicativo, adicione o namespace representando a pasta à página/exibição ou ao arquivo *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="f3453-158">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f3453-159">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="f3453-159">In the following example:</span></span>

* <span data-ttu-id="f3453-160">Mude `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3453-160">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="f3453-161">Se uma pasta chamada *Componentes* não for usada `Components` para segurar os componentes, mude para a pasta onde os componentes residem.</span><span class="sxs-lookup"><span data-stu-id="f3453-161">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="f3453-162">O arquivo *_ViewImports.cshtml* está localizado na pasta *Páginas* de um aplicativo Razor Pages ou na pasta *Views* de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="f3453-162">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="f3453-163">Para obter mais informações, consulte <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="f3453-163">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="f3453-164">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="f3453-164">Render components from a page or view</span></span>

<span data-ttu-id="f3453-165">*Esta seção diz respeito à adição de componentes a páginas ou visualizações, onde os componentes não são diretamente roteiristas a partir de solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="f3453-165">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="f3453-166">Para renderizar um componente de uma página ou exibição, use o [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f3453-166">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="f3453-167">Para obter mais informações sobre como os componentes `Component` são renderizados, o estado do componente e o Tag Helper, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="f3453-167">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
