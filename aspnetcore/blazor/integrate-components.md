---
title: Integre componentes Razor de ASP.NET Core Razor em páginas e aplicativos MVC
author: guardrex
description: Saiba mais sobre cenários de ligação de dados para componentes e Blazor elementos DOM em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: eb4378223c40594ac52f50b7b890785067515555
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771768"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integre ASP.NET Core componentes do Razor em aplicativos Razor Pages e MVC

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Os componentes do Razor podem ser integrados em aplicativos Razor Pages e MVC. Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.

Depois de [preparar o aplicativo](#prepare-the-app), use as diretrizes nas seções a seguir, dependendo dos requisitos do aplicativo:

* Componentes &ndash; roteáveis para componentes que são roteáveis diretamente das solicitações do usuário. Siga estas diretrizes para que os visitantes possam fazer uma solicitação HTTP em seu navegador para um componente com uma [`@page`](xref:mvc/views/razor#page) diretiva.
  * [Usar componentes roteáveis em um aplicativo Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Usar componentes roteáveis em um aplicativo MVC](#use-routable-components-in-an-mvc-app)
* [Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) &ndash; para componentes que não são roteáveis diretamente das solicitações do usuário. Siga estas diretrizes quando o aplicativo inserir componentes em páginas e exibições existentes com o [auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Preparar o aplicativo

Um aplicativo Razor Pages ou MVC existente pode integrar componentes Razor em páginas e exibições:

1. No arquivo de layout do aplicativo (*_Layout. cshtml*):

   * Adicione a seguinte `<base>` marcação ao `<head>` elemento:

     ```html
     <base href="~/" />
     ```

     O `href` valor (o *caminho base do aplicativo*) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz (`/`). Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do <xref:host-and-deploy/blazor/index#app-base-path> artigo.

     O arquivo *_Layout. cshtml* está localizado na pasta *páginas/compartilhada* em um aplicativo Razor pages ou *exibições/pastas compartilhadas* em um aplicativo MVC.

   * Adicione uma `<script>` marca para o script mais *incrivelmente. Server. js* imediatamente antes da marca de `</body>` fechamento:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     A estrutura adiciona o mais de um script mais *. Server. js* ao aplicativo. Não é necessário adicionar manualmente o script ao aplicativo.

1. Adicione um arquivo *_Imports. Razor* à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace`, para o namespace do aplicativo):

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

1. No `Startup.ConfigureServices`, registre o serviço de servidor mais incrivelmente:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. No `Startup.Configure`, adicione o ponto de extremidade do Hub `app.UseEndpoints`mais incrivelmente a:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integre componentes em qualquer página ou exibição. Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Usar componentes roteáveis em um aplicativo Razor Pages

*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*

Para dar suporte a componentes roteáveis do Razor em aplicativos Razor Pages:

1. Siga as orientações na seção [preparar o aplicativo](#prepare-the-app) .

1. Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:

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

1. Adicione um arquivo *_Host. cshtml* à pasta *páginas* com o seguinte conteúdo:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o `App` componente:

   * É renderizado na página.
   * É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.

   | Modo de renderização | Descrição |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o `App` componente em HTML estático e inclui um marcador para um aplicativo de servidor mais incrivelmente. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo mais incrivelmente. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um aplicativo de servidor mais incrivelmente. A `App` saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo mais incrivelmente. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o `App` componente em HTML estático. |

   Para obter mais informações sobre o auxiliar de marca de <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>componente, consulte.

1. Adicione uma rota de baixa prioridade para a página *_Host. cshtml* à configuração do ponto `Startup.Configure`de extremidade no:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Adicione componentes roteáveis ao aplicativo. Por exemplo: 

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Usar componentes roteáveis em um aplicativo MVC

*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*

Para dar suporte a componentes roteáveis do Razor em aplicativos MVC:

1. Siga as orientações na seção [preparar o aplicativo](#prepare-the-app) .

1. Adicione um arquivo *app. Razor* à raiz do projeto com o seguinte conteúdo:

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

1. Adicione um arquivo *_Host. cshtml* à pasta *views/Home* com o seguinte conteúdo:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o arquivo *_Layout. cshtml* compartilhado para seu layout.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura se o `App` componente:

   * É renderizado na página.
   * É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.

   | Modo de renderização | Descrição |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o `App` componente em HTML estático e inclui um marcador para um Blazor aplicativo de servidor. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um Blazor aplicativo de servidor. A `App` saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar Blazor um aplicativo. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o `App` componente em HTML estático. |

   Para obter mais informações sobre o auxiliar de marca de <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>componente, consulte.

1. Adicione uma ação ao controlador Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Adicione uma rota de baixa prioridade para a ação do controlador que retorna a exibição *_Host. cshtml* para a configuração do `Startup.Configure`ponto de extremidade no:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Crie uma pasta de *páginas* e adicione componentes roteáveis ao aplicativo. Por exemplo: 

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .

## <a name="render-components-from-a-page-or-view"></a>Renderizar componentes de uma página ou exibição

*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*

Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Renderizar componentes interativos com estado

Componentes interativos com estado podem ser Razor adicionados a uma página ou exibição.

Quando a página ou a exibição renderiza:

* O componente é renderizado com a página ou exibição.
* O estado inicial do componente usado para o pré-processamento é perdido.
* O novo estado do componente é criado SignalR quando a conexão é estabelecida.

A página Razor a seguir renderiza um `Counter` componente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Renderizar componentes não interativos

Na página a Razor seguir, o `Counter` componente é processado estaticamente com um valor inicial que é especificado usando um formulário. Como o componente é processado estaticamente, o componente não é interativo:

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

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Namespaces de componente

Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao arquivo *_ViewImports. cshtml* . No exemplo a seguir:

* Altere `MyAppNamespace` para o namespace do aplicativo.
* Se uma pasta chamada *Components* não for usada para manter os componentes `Components` , altere para a pasta onde residem os componentes.

```cshtml
@using MyAppNamespace.Components
```

O arquivo *_ViewImports. cshtml* está localizado na pasta *páginas* de um Razor aplicativo de páginas ou na pasta *views* de um aplicativo MVC.

Para obter mais informações, consulte <xref:blazor/components#import-components>.
