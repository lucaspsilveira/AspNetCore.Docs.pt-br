---
<span data-ttu-id="74515-101">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-102">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-103">'Blazor'</span></span>
- <span data-ttu-id="74515-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-104">'Identity'</span></span>
- <span data-ttu-id="74515-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-106">'Razor'</span></span>
- <span data-ttu-id="74515-107">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="74515-108">Integre Razor componentes de ASP.NET Core em Razor páginas e aplicativos MVC</span><span class="sxs-lookup"><span data-stu-id="74515-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="74515-109">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="74515-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="74515-110">os componentes podem ser integrados em Razor páginas e aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="74515-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="74515-111">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="74515-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="74515-112">Depois de [preparar o aplicativo](#prepare-the-app), use as diretrizes nas seções a seguir, dependendo dos requisitos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="74515-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="74515-113">Componentes roteáveis: para componentes que são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="74515-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="74515-114">Siga estas diretrizes para que os visitantes possam fazer uma solicitação HTTP em seu navegador para um componente com uma [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="74515-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="74515-115">[Usar componentes roteáveis em um Razor aplicativo de páginas](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="74515-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="74515-116">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="74515-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="74515-117">[Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view): para componentes que não são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="74515-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="74515-118">Siga estas diretrizes quando o aplicativo inserir componentes em páginas e exibições existentes com o [auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="74515-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="74515-119">Preparar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="74515-119">Prepare the app</span></span>

<span data-ttu-id="74515-120">Uma Razor página existente ou um aplicativo MVC pode integrar Razor componentes em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="74515-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="74515-121">No arquivo de layout do aplicativo (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="74515-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="74515-122">Adicione a seguinte `<base>` marcação ao `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="74515-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="74515-123">O `href` valor (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="74515-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="74515-124">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do <xref:host-and-deploy/blazor/index#app-base-path> artigo.</span><span class="sxs-lookup"><span data-stu-id="74515-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="74515-125">O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um Razor aplicativo de páginas ou em *exibições/pastas compartilhadas* em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="74515-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="74515-126">Adicione uma `<script>` marca para o script mais *incrivelmente. Server. js* imediatamente antes da marca de fechamento `</body>` :</span><span class="sxs-lookup"><span data-stu-id="74515-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="74515-127">A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="74515-128">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="74515-129">Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace` , para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="74515-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="74515-130">No `Startup.ConfigureServices` , registre o Blazor serviço do servidor:</span><span class="sxs-lookup"><span data-stu-id="74515-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="74515-131">No `Startup.Configure` , adicione o Blazor ponto de extremidade do hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="74515-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="74515-132">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="74515-132">Integrate components into any page or view.</span></span> <span data-ttu-id="74515-133">Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="74515-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="74515-134">Usar componentes roteáveis em um Razor aplicativo de páginas</span><span class="sxs-lookup"><span data-stu-id="74515-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="74515-135">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="74515-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="74515-136">Para dar suporte a componentes roteáveis Razor em Razor páginas aplicativos:</span><span class="sxs-lookup"><span data-stu-id="74515-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="74515-137">Siga as orientações na seção [preparar o aplicativo](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="74515-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="74515-138">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="74515-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="74515-139">Adicione um arquivo *_Host. cshtml* à pasta *páginas* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="74515-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="74515-140">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="74515-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="74515-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="74515-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="74515-142">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="74515-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="74515-143">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="74515-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="74515-144">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="74515-144">Render Mode</span></span> | <span data-ttu-id="74515-145">Descrição</span><span class="sxs-lookup"><span data-stu-id="74515-145">Description</span></span> |
   | ---
<span data-ttu-id="74515-146">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-147">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-148">'Blazor'</span></span>
- <span data-ttu-id="74515-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-149">'Identity'</span></span>
- <span data-ttu-id="74515-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-151">'Razor'</span></span>
- <span data-ttu-id="74515-152">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-153">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-154">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-155">'Blazor'</span></span>
- <span data-ttu-id="74515-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-156">'Identity'</span></span>
- <span data-ttu-id="74515-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-158">'Razor'</span></span>
- <span data-ttu-id="74515-159">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-160">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-161">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-162">'Blazor'</span></span>
- <span data-ttu-id="74515-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-163">'Identity'</span></span>
- <span data-ttu-id="74515-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-165">'Razor'</span></span>
- <span data-ttu-id="74515-166">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-166">'SignalR' uid:</span></span> 

<span data-ttu-id="74515-167">------ | título do---: ' integrar ASP.NET Core Razor componentes em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-168">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-169">'Blazor'</span></span>
- <span data-ttu-id="74515-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-170">'Identity'</span></span>
- <span data-ttu-id="74515-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-172">'Razor'</span></span>
- <span data-ttu-id="74515-173">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-174">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-175">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-176">'Blazor'</span></span>
- <span data-ttu-id="74515-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-177">'Identity'</span></span>
- <span data-ttu-id="74515-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-179">'Razor'</span></span>
- <span data-ttu-id="74515-180">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-181">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-182">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-183">'Blazor'</span></span>
- <span data-ttu-id="74515-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-184">'Identity'</span></span>
- <span data-ttu-id="74515-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-186">'Razor'</span></span>
- <span data-ttu-id="74515-187">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-187">'SignalR' uid:</span></span> 

<span data-ttu-id="74515-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o `App` componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="74515-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="74515-189">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="74515-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="74515-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="74515-191">A saída do `App` componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="74515-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="74515-192">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="74515-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="74515-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="74515-194">Para obter mais informações sobre o auxiliar de marca de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="74515-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="74515-195">Adicione uma rota de baixa prioridade para a página *_Host. cshtml* à configuração do ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="74515-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="74515-196">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-196">Add routable components to the app.</span></span> <span data-ttu-id="74515-197">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="74515-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="74515-198">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="74515-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="74515-199">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="74515-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="74515-200">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="74515-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="74515-201">Para dar suporte a componentes roteáveis Razor em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="74515-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="74515-202">Siga as orientações na seção [preparar o aplicativo](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="74515-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="74515-203">Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="74515-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="74515-204">Adicione um arquivo *_Host. cshtml* à pasta *views/Home* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="74515-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="74515-205">Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="74515-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="74515-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="74515-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="74515-207">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="74515-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="74515-208">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="74515-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="74515-209">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="74515-209">Render Mode</span></span> | <span data-ttu-id="74515-210">Descrição</span><span class="sxs-lookup"><span data-stu-id="74515-210">Description</span></span> |
   | ---
<span data-ttu-id="74515-211">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-212">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-213">'Blazor'</span></span>
- <span data-ttu-id="74515-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-214">'Identity'</span></span>
- <span data-ttu-id="74515-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-216">'Razor'</span></span>
- <span data-ttu-id="74515-217">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-218">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-219">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-220">'Blazor'</span></span>
- <span data-ttu-id="74515-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-221">'Identity'</span></span>
- <span data-ttu-id="74515-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-223">'Razor'</span></span>
- <span data-ttu-id="74515-224">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-225">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-226">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-227">'Blazor'</span></span>
- <span data-ttu-id="74515-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-228">'Identity'</span></span>
- <span data-ttu-id="74515-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-230">'Razor'</span></span>
- <span data-ttu-id="74515-231">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-231">'SignalR' uid:</span></span> 

<span data-ttu-id="74515-232">------ | título do---: ' integrar ASP.NET Core Razor componentes em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-233">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-234">'Blazor'</span></span>
- <span data-ttu-id="74515-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-235">'Identity'</span></span>
- <span data-ttu-id="74515-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-237">'Razor'</span></span>
- <span data-ttu-id="74515-238">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-239">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-240">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-241">'Blazor'</span></span>
- <span data-ttu-id="74515-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-242">'Identity'</span></span>
- <span data-ttu-id="74515-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-244">'Razor'</span></span>
- <span data-ttu-id="74515-245">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="74515-246">Título: ' integrar Razor componentes ASP.NET Core em Razor páginas e aplicativos MVC ' autor: Descrição: ' saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em Blazor aplicativos. '</span><span class="sxs-lookup"><span data-stu-id="74515-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="74515-247">monikerRange: MS. Author: MS. Custom: MS. Date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="74515-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="74515-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="74515-248">'Blazor'</span></span>
- <span data-ttu-id="74515-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="74515-249">'Identity'</span></span>
- <span data-ttu-id="74515-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="74515-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="74515-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="74515-251">'Razor'</span></span>
- <span data-ttu-id="74515-252">SignalRuid ' ':</span><span class="sxs-lookup"><span data-stu-id="74515-252">'SignalR' uid:</span></span> 

<span data-ttu-id="74515-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o `App` componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="74515-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="74515-254">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="74515-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="74515-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="74515-256">A saída do `App` componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="74515-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="74515-257">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="74515-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o `App` componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="74515-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="74515-259">Para obter mais informações sobre o auxiliar de marca de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="74515-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="74515-260">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="74515-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="74515-261">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a exibição *_Host. cshtml* para a configuração do ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="74515-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="74515-262">Crie uma pasta de *páginas* e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="74515-263">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="74515-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="74515-264">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="74515-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="74515-265">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="74515-265">Render components from a page or view</span></span>

<span data-ttu-id="74515-266">*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="74515-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="74515-267">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="74515-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="74515-268">Renderizar componentes interativos com estado</span><span class="sxs-lookup"><span data-stu-id="74515-268">Render stateful interactive components</span></span>

<span data-ttu-id="74515-269">Componentes interativos com estado podem ser adicionados a uma Razor página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="74515-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="74515-270">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="74515-270">When the page or view renders:</span></span>

* <span data-ttu-id="74515-271">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="74515-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="74515-272">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="74515-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="74515-273">O novo estado do componente é criado quando a SignalR conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="74515-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="74515-274">A página a seguir Razor renderiza um `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="74515-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="74515-275">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="74515-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="74515-276">Renderizar componentes não interativos</span><span class="sxs-lookup"><span data-stu-id="74515-276">Render noninteractive components</span></span>

<span data-ttu-id="74515-277">Na página a seguir Razor , o `Counter` componente é processado estaticamente com um valor inicial que é especificado usando um formulário.</span><span class="sxs-lookup"><span data-stu-id="74515-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="74515-278">Como o componente é processado estaticamente, o componente não é interativo:</span><span class="sxs-lookup"><span data-stu-id="74515-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="74515-279">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="74515-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="74515-280">Namespaces de componente</span><span class="sxs-lookup"><span data-stu-id="74515-280">Component namespaces</span></span>

<span data-ttu-id="74515-281">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao arquivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="74515-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="74515-282">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="74515-282">In the following example:</span></span>

* <span data-ttu-id="74515-283">Altere `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="74515-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="74515-284">Se uma pasta chamada *Components* não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.</span><span class="sxs-lookup"><span data-stu-id="74515-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="74515-285">O arquivo *_ViewImports. cshtml* está localizado na pasta *páginas* de um Razor aplicativo de páginas ou na pasta *views* de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="74515-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="74515-286">Para obter mais informações, consulte <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="74515-286">For more information, see <xref:blazor/components#import-components>.</span></span>
