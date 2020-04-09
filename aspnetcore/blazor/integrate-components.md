---
title: Integre ASP.NET componentes do Core Razor em páginas de barbear e aplicativos MVC
author: guardrex
description: Saiba mais sobre cenários de vinculação Blazor de dados para componentes e elementos DOM em aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218928"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integre ASP.NET componentes do Core Razor em páginas de barbear e aplicativos MVC

Por [Luke Latham](https://github.com/guardrex) e Daniel [Roth](https://github.com/danroth27)

Os componentes da navalha podem ser integrados em páginas de barbear e aplicativos MVC. Quando a página ou exibição é renderizada, os componentes podem ser pré-renderizados ao mesmo tempo.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Prepare o aplicativo para usar componentes em páginas e visualizações

Um aplicativo De navalha ou MVC existente pode integrar componentes do Razor em páginas e visualizações:

1. No arquivo de layout do aplicativo *(_Layout.cshtml*):

   * Adicione a `<base>` seguinte `<head>` tag ao elemento:

     ```html
     <base href="~/" />
     ```

     O `href` valor (o caminho base do *aplicativo)* no exemplo anterior assume que`/`o aplicativo reside no caminho raiz da URL ( ). Se o aplicativo for um subaplicativo, siga a orientação <xref:host-and-deploy/blazor/index#app-base-path> na seção de caminho base do *aplicativo* do artigo.

     O arquivo *_Layout.cshtml* está localizado na pasta *Páginas/Compartilhados* em um aplicativo Razor Pages ou na pasta *Views/Shared* em um aplicativo MVC.

   * Adicione `<script>` uma tag para o script *blazor.server.js* imediatamente antes da tag de fechamento: `</body>`

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     A estrutura adiciona o script *blazor.server.js* ao aplicativo. Não há necessidade de adicionar manualmente o script ao aplicativo.

1. Adicione um arquivo *_Imports.razor* à pasta raiz do projeto com o `MyAppNamespace`seguinte conteúdo (altere o último namespace, para o namespace do aplicativo):

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

1. Em `Startup.ConfigureServices`, Blazor registre o serviço servidor:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Em `Startup.Configure`, Blazor adicionar o `app.UseEndpoints`ponto final do Hub a:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integre os componentes em qualquer página ou exibição. Para obter mais informações, consulte os [componentes Render de uma página ou seção de exibição.](#render-components-from-a-page-or-view)

## <a name="use-routable-components-in-a-razor-pages-app"></a>Use componentes roteáveis em um aplicativo Razor Pages

*Esta seção diz respeito à adição de componentes que são diretamente roteiráveis a partir de solicitações do usuário.*

Para suportar componentes de navalha routable em aplicativos Razor Pages:

1. Siga a orientação no Prepare o aplicativo para usar componentes na seção [páginas e visualizações.](#prepare-the-app-to-use-components-in-pages-and-views)

1. Adicione um arquivo *App.razor* à raiz do projeto com o seguinte conteúdo:

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

1. Adicione um arquivo *_Host.cshtml* à pasta *Páginas* com o seguinte conteúdo:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o arquivo *_Layout.cshtml* compartilhado para seu layout.

1. Adicione uma rota de baixa prioridade para a página *_Host.cshtml* para a configuração de ponto final em `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Adicione componentes rotáveis ao aplicativo. Por exemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Para obter mais informações sobre namespaces, consulte a seção [Espaços de nomes componentes.](#component-namespaces)

## <a name="use-routable-components-in-an-mvc-app"></a>Use componentes roteáveis em um aplicativo MVC

*Esta seção diz respeito à adição de componentes que são diretamente roteiráveis a partir de solicitações do usuário.*

Para suportar componentes de navalha routable em aplicativos MVC:

1. Siga a orientação no Prepare o aplicativo para usar componentes na seção [páginas e visualizações.](#prepare-the-app-to-use-components-in-pages-and-views)

1. Adicione um arquivo *App.razor* à raiz do projeto com o seguinte conteúdo:

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

1. Adicione um arquivo *_Host.cshtml* à pasta *Views/Home* com o seguinte conteúdo:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o arquivo *_Layout.cshtml* compartilhado para seu layout.

1. Adicione uma ação ao controlador Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Adicione uma rota de baixa prioridade para a ação do controlador que retorna `Startup.Configure`a exibição *_Host.cshtml* à configuração do ponto final em :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Crie uma pasta *Páginas* e adicione componentes rotáveis ao aplicativo. Por exemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Para obter mais informações sobre namespaces, consulte a seção [Espaços de nomes componentes.](#component-namespaces)

## <a name="component-namespaces"></a>Espaços de nomes de componentes

Ao usar uma pasta personalizada para segurar os componentes do aplicativo, adicione o namespace representando a pasta à página/exibição ou ao arquivo *_ViewImports.cshtml.* No exemplo a seguir:

* Mude `MyAppNamespace` para o namespace do aplicativo.
* Se uma pasta chamada *Componentes* não for usada `Components` para segurar os componentes, mude para a pasta onde os componentes residem.

```cshtml
@using MyAppNamespace.Components
```

O arquivo *_ViewImports.cshtml* está localizado na pasta *Páginas* de um aplicativo Razor Pages ou na pasta *Views* de um aplicativo MVC.

Para obter mais informações, consulte <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Renderizar componentes de uma página ou exibição

*Esta seção diz respeito à adição de componentes a páginas ou visualizações, onde os componentes não são diretamente roteiristas a partir de solicitações do usuário.*

Para renderizar um componente de uma página ou exibição, use o [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Para obter mais informações sobre como os componentes `Component` são renderizados, o estado do componente e o Tag Helper, consulte os seguintes artigos:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
