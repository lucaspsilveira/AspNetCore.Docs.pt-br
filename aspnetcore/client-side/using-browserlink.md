---
title: Link do navegador no núcleo ASP.NET
author: ncarandini
description: Explica como o Browser Link é um recurso do Visual Studio que conecta o ambiente de desenvolvimento com um ou mais navegadores da Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658847"
---
# <a name="browser-link-in-aspnet-core"></a>Link do navegador no núcleo ASP.NET

Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), e [Tom Dykstra](https://github.com/tdykstra)

Browser Link é um recurso do Visual Studio. Ele cria um canal de comunicação entre o ambiente de desenvolvimento e um ou mais navegadores da Web. Você pode usar o Browser Link para atualizar seu aplicativo web em vários navegadores ao mesmo tempo, o que é útil para testes entre navegadores.

## <a name="browser-link-setup"></a>Configuração do link do navegador

::: moniker range=">= aspnetcore-3.0"

Adicione o pacote [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ao seu projeto. Para ASP.NET páginas de navalha principais ou projetos MVC, também habilite <xref:mvc/views/view-compilation>a compilação em tempo de execução de arquivos Razor (*.cshtml)* conforme descrito em . As alterações de sintaxe de navalha são aplicadas somente quando a compilação em tempo de execução foi ativada.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Ao converter um projeto ASP.NET Core 2.0 para ASP.NET Core 2.1 e fazer a transição para o [metapacote Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)instale o pacote [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para a funcionalidade do Browser Link. Os modelos de projeto ASP.NET `Microsoft.AspNetCore.App` Core 2.1 usam o metapacote por padrão.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Os modelos de projeto de ASP.NET Core 2.0 **Web Application,** **Empty**e **Web API** usam o [metapacote Microsoft.AspNetCore.All,](xref:fundamentals/metapackage)que contém uma referência de pacote para [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Portanto, o `Microsoft.AspNetCore.All` uso do metapacote não requer mais nenhuma ação para disponibilizar o Browser Link para uso.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

O modelo de projeto do ASP.NET Core 1.x **Do Aplicativo da Web** tem uma referência de pacote para o pacote [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Outros tipos de projeto exigem que `Microsoft.VisualStudio.Web.BrowserLink`você adicione uma referência de pacote a .

::: moniker-end

### <a name="configuration"></a>Configuração

Chame `UseBrowserLink` no método `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

A `UseBrowserLink` chamada é normalmente `if` colocada dentro de um bloco que só habilita o Link do Navegador no ambiente Desenvolvimento. Por exemplo:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Para obter mais informações, consulte <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Como usar o Link do Navegador

Quando você tem um projeto ASP.NET Core aberto, o Visual Studio mostra o controle da barra de ferramentas do Browser Link ao lado do controle da barra de ferramentas **Debug Target:**

![Menu suspenso do Link do navegador](using-browserlink/_static/browserLink-dropdown-menu.png)

A partir do controle da barra de ferramentas do Browser Link, você pode:

* Atualize o aplicativo web em vários navegadores ao mesmo tempo.
* Abra o **painel de link do navegador**.
* Habilitar ou desativar **o Link do Navegador**. Nota: O Link do Navegador é desativado por padrão no Visual Studio.
* Ativar ou desativar [o CSS Auto-Sync](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Atualize o aplicativo web em vários navegadores ao mesmo tempo

Para escolher um único navegador da Web para iniciar ao iniciar o projeto, use o menu suspenso no controle da barra de ferramentas **Debug Target:**

![Menu suspenso F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Para abrir vários navegadores ao mesmo tempo, escolha **Procurar com...** do mesmo drop-down. Mantenha a tecla <kbd>Ctrl</kbd> para selecionar os navegadores desejados e clique **em Procurar**:

![Abra muitos navegadores ao mesmo tempo](using-browserlink/_static/open-many-browsers-at-once.png)

A captura de tela a seguir mostra o Visual Studio com a exibição Index aberta e dois navegadores abertos:

![Sincronizar com dois navegadores exemplo](using-browserlink/_static/sync-with-two-browsers-example.png)

Passar o passar o tempo no controle da barra de ferramentas do Browser Link para ver os navegadores conectados ao projeto:

![Ponta de hover](using-browserlink/_static/hoover-tip.png)

Alterar a exibição Índice e todos os navegadores conectados são atualizados quando você clica no botão de atualização do Link do navegador:

![navegadores-sincronização para alterações](using-browserlink/_static/browsers-sync-to-changes.png)

O Browser Link também funciona com navegadores que você inicia de fora do Visual Studio e navega até a URL do aplicativo.

### <a name="the-browser-link-dashboard"></a>O painel de link do navegador

Abra a janela **browser link dashboard** a partir do menu suspenso do Link do navegador para gerenciar a conexão com navegadores abertos:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Se nenhum navegador estiver conectado, você pode iniciar uma sessão de não depuração selecionando o link **Exibir no navegador:**

![navegadorlink-dashboard-sem-conexões](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Caso contrário, os navegadores conectados são mostrados com o caminho para a página que cada navegador está mostrando:

![navegadorlink-dashboard-duas conexões](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Você também pode clicar em um nome de navegador individual para atualizar apenas esse navegador.

### <a name="enable-or-disable-browser-link"></a>Habilitar ou desativar o Link do Navegador

Quando você reativar o Browser Link depois de desativá-lo, você deve atualizar os navegadores para reconectá-los.

### <a name="enable-or-disable-css-auto-sync"></a>Ativar ou desativar o CSS Auto-Sync

Quando o CSS Auto-Sync é ativado, os navegadores conectados são atualizados automaticamente quando você faz qualquer alteração em arquivos CSS.

## <a name="how-it-works"></a>Como ele funciona

O Browser [SignalR](xref:signalr/introduction) Link usa para criar um canal de comunicação entre o Visual Studio e o navegador. Quando o Browser Link é ativado, SignalR o Visual Studio atua como um servidor ao que vários clientes (navegadores) podem se conectar. O Browser Link também registra um componente de middleware no pipeline de solicitação do ASP.NET Core. Este componente injeta `<script>` referências especiais em cada solicitação de página do servidor. Você pode ver as referências do script selecionando **'Exibir origem'** no navegador e rolando até o final do conteúdo da `<body>` tag:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Seus arquivos de origem não foram modificados. O componente middleware injeta as referências do script dinamicamente.

Como o código do lado do navegador é todo SignalR JavaScript, ele funciona em todos os navegadores que suportam sem exigir um plug-in do navegador.
