---
title: ASP.NET Blazor modelos Core
author: guardrex
description: Conheça ASP.NET Blazor modelos de Blazor aplicativos Core e estrutura do projeto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 71a9d9eee8637dda0b3cecac82ff96a0c3bfedb5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320978"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Blazor modelos Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

A Blazor estrutura fornece modelos para desenvolver Blazor aplicativos para cada um dos modelos de hospedagem:

* BlazorWebAssembly`blazorwasm`( )
* BlazorServidor`blazorserver`( )

Para obter Blazormais informações sobre <xref:blazor/hosting-models>os modelos de hospedagem, consulte .

Para obter instruções passo a Blazor passo sobre a <xref:blazor/get-started>criação de um aplicativo a partir de um modelo, consulte .

## <a name="opno-locblazor-project-structure"></a>Blazorestrutura do projeto

Os seguintes arquivos e Blazor pastas compõem um aplicativo gerado a partir de um Blazor modelo:

* *Program.cs* &ndash; O ponto de entrada do aplicativo que configura:

  * host [host](xref:fundamentals/host/generic-host) ASP.NETBlazor Core (Servidor)
  * Host WebAssemblyBlazor (WebAssembly) &ndash; O código neste arquivo é Blazor exclusivo para`blazorwasm`aplicativos criados a partir do modelo WebAssembly ( ).
    * O `App` componente, que é o componente raiz do `app` aplicativo, é `Add` especificado como o elemento DOM para o método.
    * Os serviços podem `ConfigureServices` ser configurados com o `builder.Services.AddSingleton<IMyDependency, MyDependency>();`método no construtor de host (por exemplo, ).
    * A configuração pode ser`builder.Configuration`fornecida através do construtor host ( ).

* *Startup.cs* Blazor (Servidor) &ndash; Contém a lógica de inicialização do aplicativo. A `Startup` classe define dois métodos:

  * `ConfigureServices`&ndash; Configura os serviços de [injeção de dependência (DI)](xref:fundamentals/dependency-injection) do aplicativo. Nos Blazor aplicativos server, os <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>serviços `WeatherForecastService` são adicionados por chamada , `FetchData` e o é adicionado ao contêiner de serviço para uso pelo componente exemplo.
  * `Configure`&ndash; Configura o pipeline de tratamento de solicitação do aplicativo:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>é chamado para configurar um ponto final para a conexão em tempo real com o navegador. A conexão é [SignalR](xref:signalr/introduction)criada com , que é uma estrutura para adicionar funcionalidade web em tempo real aos aplicativos.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo *(Pages/_Host.cshtml)* e ativar a navegação.

* *wwwroot/index.html* Blazor ( WebAssembly) &ndash; A página raiz do aplicativo implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, esta página é renderizada e devolvida na resposta.
  * A página especifica onde `App` o componente raiz é renderizado. O `App` componente *(App.razor)* é `app` especificado como `AddComponent` o `Startup.Configure`elemento DOM para o método em .
  * O `_framework/blazor.webassembly.js` arquivo JavaScript está carregado, o que:
    * Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
    * Inicializa o tempo de execução para executar o aplicativo.

* *App.razor* &ndash; O componente raiz do aplicativo que configura roteamento ao lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. O `Router` componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

* *Pasta* &ndash; páginas Contém os componentes/páginas routable *(.razor)* que compõem Blazor Blazor o aplicativo e a página navalha raiz de um aplicativo server. A rota para cada página [`@page`](xref:mvc/views/razor#page) é especificada usando a diretiva. O modelo inclui o seguinte:
  * *_Host.cshtml* Blazor (Servidor) &ndash; A página raiz do aplicativo implementada como uma página de navalha:
    * Quando qualquer página do aplicativo é solicitada inicialmente, esta página é renderizada e devolvida na resposta.
    * O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que SignalR configura a conexão em tempo real entre o navegador e o servidor.
    * A página Host especifica `App` onde o componente raiz *(App.razor)* é renderizado.
  * `Counter`*(Counter.razor* &ndash; ) Implementa a página Contador.
  * `Error`(*Erro.razor*, Blazor somente aplicativo do servidor) &ndash; Renderizado quando uma exceção não tratada ocorre no aplicativo.
  * `FetchData`*(FetchData.razor)* &ndash; Implementa a página de dados do Fetch.
  * `Index`*(Index.razor)* &ndash; Implementa a home page.

* *Pasta* compartilhada &ndash; Contém outros componentes de IU *(.razor)* usados pelo aplicativo:
  * `MainLayout`(*MainLayout.razor*) &ndash; O componente de layout do aplicativo.
  * `NavMenu`(*NavMenu.razor*) &ndash; Implementa navegação de barra lateral. Inclui o [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que renderiza links de navegação para outros componentes da Navalha. O `NavLink` componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente é exibido atualmente.

* *_Imports.razor* &ndash; Inclui diretivas comuns de Razor para incluir nos componentes [`@using`](xref:mvc/views/razor#using) do aplicativo *(.razor),* como diretivas para namespaces.

* *Pasta* deBlazor dados &ndash; (Servidor) Contém `WeatherForecast` `WeatherForecastService` a classe e a implementação `FetchData` dos que fornecem dados meteorológicos de exemplo para o componente do aplicativo.

* *wwwroot* &ndash; A pasta [Web Root](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.

* *configurações de appsettings.json* (Servidor)Blazor &ndash; Configurações para o aplicativo.
