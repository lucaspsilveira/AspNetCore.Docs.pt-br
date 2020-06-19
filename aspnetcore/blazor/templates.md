---
title: Modelos de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre os Blazor modelos de aplicativo ASP.NET Core e a Blazor estrutura do projeto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 3a491e43aec8291fcf03696b53240bd33e0deda1
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102493"
---
# <a name="aspnet-core-blazor-templates"></a>Modelos de ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

A Blazor estrutura fornece modelos para desenvolver aplicativos para cada um dos Blazor modelos de hospedagem:

* BlazorWebassembly ( `blazorwasm` )
* BlazorServidor ( `blazorserver` )

Para obter mais informações sobre os Blazor modelos de hospedagem do, consulte <xref:blazor/hosting-models> .

Para obter as instruções passo a passo sobre como criar um Blazor aplicativo a partir de um modelo, consulte <xref:blazor/get-started> .

As opções de modelo estão disponíveis passando a `--help` opção para o [novo comando dotnet](/dotnet/core/tools/dotnet-new) da CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorestrutura do projeto

Os seguintes arquivos e pastas compõem um Blazor aplicativo gerado a partir de um Blazor modelo:

* *Program.cs*: o ponto de entrada do aplicativo que configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor servidor)
  * Host Webassembly ( Blazor Webassembly): o código nesse arquivo é exclusivo para aplicativos criados a partir do Blazor modelo Webassembly ( `blazorwasm` ).
    * O `App` componente, que é o componente raiz do aplicativo, é especificado como o `app` elemento DOM para o `Add` método.
    * Os serviços podem ser configurados com o `ConfigureServices` método no construtor de hosts (por exemplo, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * A configuração pode ser fornecida por meio do host Builder ( `builder.Configuration` ).

* *Startup.cs* ( Blazor servidor): contém a lógica de inicialização do aplicativo. A `Startup` classe define dois métodos:

  * `ConfigureServices`: Configura os serviços de injeção de [dependência (di)](xref:fundamentals/dependency-injection) do aplicativo. Em Blazor aplicativos de servidor, os serviços são adicionados chamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e o `WeatherForecastService` é adicionado ao contêiner de serviço para ser usado pelo componente de exemplo `FetchData` .
  * `Configure`: Configura o pipeline de tratamento de solicitação do aplicativo:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>é chamado para configurar um ponto de extremidade para a conexão em tempo real com o navegador. A conexão é criada com [SignalR](xref:signalr/introduction) , que é uma estrutura para adicionar funcionalidade da Web em tempo real a aplicativos.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) é chamado para configurar a página raiz do aplicativo (*pages/_Host. cshtml*) e habilitar a navegação.

* *wwwroot/index.html* ( Blazor Webassembly): a página raiz do aplicativo é implementada como uma página HTML:
  * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
  * A página especifica onde o `App` componente raiz é renderizado. O `App` componente (*app. Razor*) é especificado como o `app` elemento DOM para o `AddComponent` método em `Startup.Configure` .
  * O `_framework/blazor.webassembly.js` arquivo JavaScript é carregado, que:
    * Baixa o tempo de execução do .NET, o aplicativo e as dependências do aplicativo.
    * Inicializa o tempo de execução para executar o aplicativo.

* *App. Razor*: o componente raiz do aplicativo que configura o roteamento do lado do cliente usando o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercepta a navegação do navegador e renderiza a página que corresponde ao endereço solicitado.

* Pasta de *páginas* : contém os componentes/páginas roteáveis (*. Razor*) que compõem o Blazor aplicativo e a Razor página raiz de um Blazor aplicativo de servidor. A rota para cada página é especificada usando a [`@page`](xref:mvc/views/razor#page) diretiva. O modelo inclui o seguinte:
  * *_Host. cshtml* ( Blazor servidor): a página raiz do aplicativo implementada como uma Razor página:
    * Quando qualquer página do aplicativo é solicitada inicialmente, essa página é renderizada e retornada na resposta.
    * O `_framework/blazor.server.js` arquivo JavaScript é carregado, o que configura a conexão em tempo real SignalR entre o navegador e o servidor.
    * A página host especifica onde o `App` componente raiz (*app. Razor*) é renderizado.
  * `Counter`(*Counter. Razor*): implementa a página do contador.
  * `Error`(*Error. Razor*, Blazor Somente aplicativo de servidor): renderizado quando ocorre uma exceção sem tratamento no aplicativo.
  * `FetchData`(*FetchData. Razor*): implementa a página de busca de dados.
  * `Index`(*Index. Razor*): implementa a Home Page.

* Pasta *compartilhada* : contém outros componentes de interface do usuário (*. Razor*) usados pelo aplicativo:
  * `MainLayout`(*MainLayout. Razor*): o componente de layout do aplicativo.
  * `NavMenu`(*NavMenu. Razor*): implementa a navegação na barra lateral. Inclui o [componente NavLink](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), que renderiza links de navegação para outros Razor componentes. O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente um estado selecionado quando seu componente é carregado, o que ajuda o usuário a entender qual componente está sendo exibido no momento.

* *_Imports. Razor*: inclui Razor diretivas comuns para incluir nos componentes do aplicativo (*. Razor*), como [`@using`](xref:mvc/views/razor#using) diretivas para namespaces.

* Pasta de *dados* ( Blazor servidor): contém a `WeatherForecast` classe e a implementação do `WeatherForecastService` que fornecem dados meteorológicos de exemplo para o componente do aplicativo `FetchData` .

* *wwwroot*: a pasta [raiz da Web](xref:fundamentals/index#web-root) para o aplicativo que contém os ativos estáticos públicos do aplicativo.

* *appsettings.jsem* ( Blazor servidor): definições de configuração para o aplicativo.
