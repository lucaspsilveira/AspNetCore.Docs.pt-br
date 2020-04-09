---
title: Comece com ASP.NET CoreBlazor
author: guardrex
description: Comece construindo Blazor um Blazor aplicativo com a ferramenta de sua escolha.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: c49209afde21046a6bc0b197cc4b8d93b164b23e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471820"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Comece com ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Para começar com Blazor, siga as orientações para a sua escolha de ferramentaria:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Para criar aplicativos Blazor Server, instale a versão mais recente do [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) com a carga de trabalho **de ASP.NET e desenvolvimento web.**

   Para criar aplicativos Blazor Server e Blazor WebAssembly, instale a última pré-visualização do [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) com a **carga de trabalho de desenvolvimento ASP.NET e web.**

   Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor WebAssembly* e *Blazor Server*, consulte .

1. Criar um novo projeto.

1. Selecione **o aplicativo Blazor**. Selecione **Avançar**.

1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Confirme se a entrada **do local** está correta ou forneça um local para o projeto. Selecione **Criar**.

1. Para uma experiência blazor WebAssembly (Visual Studio 16.6 Preview 2 ou posterior), escolha o modelo **do Aplicativo Blazor WebAssembly.** Para uma experiência de Servidor Blazor (Visual Studio 16.4 ou posterior), escolha o modelo **do Aplicativo Blazor Server.** Selecione **Criar**.

1. Pressione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para executar o aplicativo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Instale o [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Instale opcionalmente o modelo de visualização do [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > A [versão 3.NET Core SDK 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessária** para usar o modelo 3.2 Preview 3 Blazor WebAssembly. Confirme a versão instalada do .NET `dotnet --version` Core SDK executando em um shell de comando.

1. Instale [o Visual Studio Code](https://code.visualstudio.com/).

1. Instale a mais recente [extensão C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e a extensão [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) com `debug.javascript.usePreview` set para `true`.

1. Para uma experiência de Servidor Blazor, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Para uma experiência blazor WebAssembly, execute o seguinte comando em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor Server* e *Blazor WebAssembly*, consulte .

1. Abra a pasta *WebApplication1* no Visual Studio Code.

1. O IDE solicita que você adicione ativos para construir e depurar o projeto. Selecione **Sim**.

1. Execute o aplicativo usando o depurador visual studio code.

1. Em um navegador, navegue até `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Blazor Server é suportado no Visual Studio para Mac. Blazor WebAssembly não é suportado no momento. Para criar aplicativos Blazor WebAssembly no macOS, siga as orientações na guia **.NET Core CLI.**

1. Instale [o Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Selecione **'Arquivo** > **nova solução'** ou crie um **novo projeto**.

1. Na barra lateral, selecione **.NET Core** > **App**.

1. Selecione o modelo **do Aplicativo Do Servidor Blazor.** Selecione **Criar**.

   Para obter informações sobre o modelo <xref:blazor/hosting-models>de hospedagem do Blazor Server, consulte .

1. Defina o **Framework de destino** para **.NET Core 3.1** e selecione **Next**.

1. No campo Nome do **projeto,** nomeie o aplicativo `WebApplication1`. Selecione **Criar**.

1. Selecione **Executar** > **executar sem depuração** para executar o aplicativo *sem o depurador*. Execute o aplicativo com **Iniciar Depuração** para executar o aplicativo *com o depurador*.

Se um prompt aparecer para confiar no certificado de desenvolvimento, confie no certificado e continue.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

1. Instale o [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Instale opcionalmente o modelo de visualização do [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) executando o seguinte comando:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > A [versão 3.NET Core SDK 3.1.201 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) é **necessária** para usar o modelo 3.2 Preview 3 Blazor WebAssembly. Confirme a versão instalada do .NET `dotnet --version` Core SDK executando em um shell de comando.

1. Para uma experiência de Servidor Blazor, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para uma experiência blazor WebAssembly, execute os seguintes comandos em um shell de comando:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Para obter informações sobre os dois modelos de hospedagem <xref:blazor/hosting-models>blazor, *Blazor Server* e *Blazor WebAssembly*, consulte .

1. Em um navegador, navegue até `https://localhost:5001`.

---

Várias páginas estão disponíveis nas guias na barra lateral:

* Home
* Contador
* Buscar dados

Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. Incrementar um contador em uma página da Web Blazor normalmente requer escrever JavaScript, mas com você pode usar C#.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Uma solicitação `/counter` no navegador, conforme especificado pela `@page` diretiva na `Counter` parte superior, faz com que o componente torne seu conteúdo. Os componentes se transformam em uma representação na memória da árvore de renderização que pode ser usada para atualizar a iu de forma flexível e eficiente.

Cada vez que o botão **Clique em mim** é selecionado:

* O `onclick` evento foi disparado.
* O método `IncrementCount` é chamado.
* O `currentCount` é incrementado.
* O componente é renderizado novamente.

O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado ao DoM (Document Object Model).

Adicione um componente a outro componente usando sintaxe HTML. Por exemplo, `Counter` adicione o componente à página inicial `<Counter />` do `Index` aplicativo adicionando um elemento ao componente.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Execute o aplicativo. A página inicial tem seu próprio `Counter` contador fornecido pelo componente.

Os parâmetros dos componentes são especificados usando atributos ou [conteúdo infantil,](xref:blazor/components#child-content)que permitem definir propriedades no componente filho. Para adicionar um parâmetro `Counter` ao componente, atualize o bloco do `@code` componente:

* Adicione uma propriedade `IncrementAmount` pública `[Parameter]` para com um atributo.
* Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Especifique o `IncrementAmount` `<Counter>` elemento no `Index` componente usando um atributo.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Execute o aplicativo. O `Index` componente tem seu próprio contador que incrementa por dez cada vez que o botão **Clique-me** é selecionado. O `Counter` componente *(Counter.razor)* continua `/counter` a incrementar por um.

## <a name="next-steps"></a>Próximas etapas

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/templates>
* <xref:signalr/introduction>
