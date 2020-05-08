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
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967422"
---
# <a name="aspnet-core-blazor-templates"></a>Modelos Blazor de ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

A Blazor estrutura fornece modelos para desenvolver aplicativos para cada um dos Blazor modelos de hospedagem:

* BlazorWebassembly (`blazorwasm`)
* BlazorServidor (`blazorserver`)

Para obter mais informações Blazorsobre os modelos de hospedagem <xref:blazor/hosting-models>do, consulte.

Para obter as instruções passo a passo sobre como criar Blazor um aplicativo a partir de um <xref:blazor/get-started>modelo, consulte.

As opções de modelo estão disponíveis passando `--help` a opção para o [novo comando dotnet](/dotnet/core/tools/dotnet-new) da CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorestrutura do projeto

Os seguintes arquivos e pastas compõem um Blazor aplicativo gerado a partir Blazor de um modelo:

* *Program.cs* &ndash; o ponto de entrada do aplicativo que configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor servidor)
  * Host Webassembly (Blazor Webassembly) &ndash; o código nesse arquivo é exclusivo para aplicativos criados a Blazor partir do modelo Webassembly (`blazorwasm`).
    * O `App` componente, que é o componente raiz do aplicativo, é especificado como o `app` elemento DOM para o `Add` método.
    * Os serviços podem ser configurados com o `ConfigureServices` método no construtor de hosts `builder.Services.AddSingleton<IMyDependency, MyDependency>();`(por exemplo,).
    * A configuração pode ser fornecida por meio do host`builder.Configuration`Builder ().

* *Startup.cs* (Blazor servidor) &ndash; contém a lógica de inicialização do aplicativo. A `Startup` classe define dois métodos:

  * `ConfigureServices`&ndash; Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo. Em Blazor aplicativos de servidor, os serviços são adicionados <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>chamando e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente `FetchData` de exemplo.
  * `Configure`&ndash; Configura o pipeline de tratamento de solicitação do aplicativo:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador. A conexão é criada com [SignalR](xref:signalr/introduction), que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo (*pages/_Host. cshtml*) e habilitar a navegação.

* *wwwroot/index.html* (Blazor Webassembly) &ndash; a página raiz do aplicativo implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * A página especifica onde o componente `App` raiz é renderizado. O `App` componente (*app. Razor*) é especificado como o `app` elemento DOM para o `AddComponent` método em `Startup.Configure`.
  * O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:
    * Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
    * Inicializa o tempo de execução para executar o aplicativo.

* *App. Razor* &ndash; o componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. O `Router` componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

* *Pages* A pasta &ndash; páginas contém os componentes/páginas roteáveis (*. Razor*) que compõem o Blazor aplicativo e Razor a página raiz Blazor de um aplicativo de servidor. A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva. O modelo inclui o seguinte:
  * *_Host. cshtml* (Blazor servidor) &ndash; a página raiz do aplicativo implementada como uma Razor página:
    * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
    * O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que configura a conexão em tempo SignalR real entre o navegador e o servidor.
    * A página host especifica onde o componente `App` raiz (*app. Razor*) é renderizado.
  * `Counter`(*Counter. Razor*) &ndash; implementa a página do contador.
  * `Error`(*Error. Razor*, Blazor somente aplicativo de servidor) &ndash; Renderizado quando ocorre uma exceção sem tratamento no aplicativo.
  * `FetchData`(*FetchData. Razor*) &ndash; implementa a página buscar dados.
  * `Index`(*Index. Razor*) &ndash; implementa a Home Page.

* *Shared* A pasta &ndash; compartilhada contém outros componentes de interface do usuário (*. Razor*) usados pelo aplicativo:
  * `MainLayout`(*MainLayout. Razor*) &ndash; o componente de layout do aplicativo.
  * `NavMenu`(*NavMenu. Razor*) &ndash; implementa a navegação na barra lateral. Inclui o [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), que renderiza links de navegação para outros Razor componentes. O `NavLink` componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.

* *_Imports. Razor* &ndash; inclui diretivas Razor comuns para incluir nos componentes do aplicativo (*. Razor*), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.

* *Data* A pasta deBlazor dados ( &ndash; servidor) `WeatherForecast` contém a classe e a `WeatherForecastService` implementação do que fornecem dados meteorológicos de exemplo `FetchData` para o componente do aplicativo.

* *wwwroot* &ndash; a pasta [raiz da Web](xref:fundamentals/index#web-root) do aplicativo que contém os ativos estáticos públicos do aplicativo.

* parâmetros de configuração *appSettings. JSON* (Blazor Server) &ndash; para o aplicativo.
